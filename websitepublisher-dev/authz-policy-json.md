# AuthZ Governance — the `policy_json` reference (#942)

> Reference material split out of the dev skill (v2.1) to keep that file inside its size
> budget. The dev skill carries the working rules — when policies fail, how they are
> tested, and the HTTP contract. **This file is the lookup:** grammar, worked examples,
> delivery paths and the verification log.
>
> Read the dev skill first: it tells you *when* you need this. This file tells you *what to
> write*.

Row-level entity access control is a **declarative `policy_json` block per entity**, enforced at the
unbypassable data-layer chokepoint (`MapiEntityHelper` → `MapiPolicyGate` → `AccessPolicy`). This is
the reference the customer skill points to.

**Opt-in per entity.** An entity with **no** `policy_json` is ungoverned — the gate is a byte-identical
pass-through (`public_read` still controls anonymous visibility). Enforcement activates **only** when an
entity carries a `policy_json`. Strict mode (governed-by-default) is the end state; design sensitive
entities with an explicit policy now.

## Setting a policy

Set / change / clear via the `entities` tool (operation `update`):

```
entities(operation: "update", entity_name: "customer", policy_json: { … })   // activate / change
entities(operation: "update", entity_name: "customer", policy_json: null)    // deactivate → reverts to public_read behaviour
entities(operation: "schema", entity_name: "customer")                       // inspect current policy
```

The deprecated granular tools (`update_entity`, `create_entity`) do **not** carry a `policy_json`
parameter — always use `entities`. The server validates that the JSON is well-formed, **not** that the
rules are semantically correct.

## Grammar

**Grants** (per action, per tier): `deny` | `all` | `own` | `self`

- `deny` — no access.
- `all` — every row (privileged).
- `own` — only rows where `owner_field` == the caller's identity.
- `self` — on `create`, stamp `owner_field` = caller identity (degrades to `own` on any non-create action).

**Tiers** (`AccessPolicy::VALID_TIERS`): `project`, `site_admin`, `verified`, `anon`, `public`.
`PRIV = {project, site_admin}` default to `all` unless a rule explicitly lists another grant (an explicit
`deny` on a privileged tier still wins). Every other tier defaults to `deny`.

Identity mapping (`MapiPolicyGate`): owner → `project`; verified SAPI visitor → `verified` (identity =
email); `wst_` tenant → `verified` (identity = per-user scope, or the bare `tenant_code` under
`owner_scope:"tenant"`); unknown → `public`. Non-owner identity is set only on the SAPI route
(`SapiExecuteController`); raw MAPI HTTP (`MapiAuthenticate`) is owner-only (`mapi.principal` = v2).

**Rules** — keys `read`/`list`/`create`/`update`/`delete`, each mapping tier → grant. Always specify
`"project": "all"`. An omitted non-privileged tier = `deny`.

**`owner_field`** — must be a **real entity property (column)**; the engine fails closed (HTTP 500) on a
phantom field.

**`fields.<tier>.hide[]`** — columns projected out (hidden) from that tier on read/list.

**State guards** (optional) — `"guards": {"update": {"when": "status in (draft, pending)"}}`. Only the
`field in (a, b)` form is evaluated; anything else is treated as permissive. Guards bind all tiers,
including owner/admin.

**`owner_scope`** (optional — #942 org-wide tenant) — `"owner_scope": "tenant"` makes the ownership
identity the **bare `tenant_code`** for a tenant caller, so all users of a tenant share visibility of
that tenant's rows (org-wide isolation for sub-tenancy inside one project). Requires a real `tenant_code`
column and `wst_` tenant sessions. A non-tenant caller resolves to a null identity and is denied. Absent
= per-caller identity (email for visitors, per-user scope for tenants).

## Delivery path — read this before configuring a tenant policy

A tenant-scoped entity is only reachable through a service that runs under a tenant
identity. Two exist: **`gated-files`** (file delivery) and, since September 2026, the
**`records`** bridge (#1310) — `POST /sapi/project/{id}/execute/records/{list|get}`,
read-only, and it refuses any entity without a `policy_json` because an ungoverned
entity is a gate pass-through.

**Verified end-to-end, 1 September 2026.** Tested from outside the cluster against a live
tenant session: two users of one tenant read the same rows — rows created by the *owner*,
so nothing carried a per-user stamp, which is what proves the match runs on the bare
`tenant_code` and not on `tenant:{code}:{uid}`. A member of another tenant sees only their
own; a cross-tenant record returns 403; a session without a `wst_` Bearer returns 401;
`fields.verified.hide[]` columns stay out of the response; and `pagination.total` is
scoped too (2 and 1, versus 3 as owner), so the count runs through the same gate.

**Error shape — read this before writing browser code.** Only what the controller itself
rejects (no session, missing CSRF) returns a real HTTP status. Everything the integration
refuses — 403, 404, 422, 409 — arrives as **HTTP 200** with `success: false` and an
`upstream_status`. Branching on `res.ok` turns a permission denial into a silent empty
render. Check `success`.

## HTTP contract

- `deny` (tier not granted the action) → **404** (no existence leak).
- `own`-mismatch (row not owned by the caller) → **403**.
- state guard failed → **409**.

## `public_read` interaction

The implied public-read shortcut fires **only when there is no policy block**
(`policy === null && public_read`). Once an entity carries any `policy_json`, `public_read` is **ignored
entirely** — grants come purely from `rules`. Sensitive entities: set the policy and keep
`public_read: false`.

## SSR is never a path for governed data

`<!--#wps-mapi -->` server-side rendering reads **only** entities with `public_read: true`, and its
render cache is keyed on `website_id` + entity name — **no session dimension**. One shared cache serves
every visitor of that page.

That makes SSR structurally unusable for governed or tenant-scoped data, and it stays that way after
#1310 — this is the nature of the layer, not a gap to close. Gated content is fetched client-side from
a verified session via the SAPI execute route.

Side effect worth knowing: when the entity is not public the whole `wps-mapi` block is stripped from the
output, **including the `wps-mapi-empty` branch**, and the negative result is cached for the full TTL.
No error surfaces anywhere. An empty block where you expected data almost always means
`public_read: false`, not "no records" (#1311).

## Enforcement boundary

Owner/admin (`wsa_`) sessions run at `project` tier (`all`), so admin panels and the data grid work on
governed entities with no extra wiring. **Always test a policy activation on a sandbox entity in a
sandbox project before flipping `policy_json` on a live table** — the server validates JSON shape, not
rule correctness, so a wrong `owner_field` or over-permissive rule is accepted.

> **Testing as owner does not exercise the policy at all.**
> An owner runs at tier `project` with grant `all`, and `all` performs no ownership matching — so
> `owner_field` is never read. A misspelled or non-existent `owner_field` therefore returns rows
> normally in an owner test, and only fails (fail-closed, HTTP 500) once a real `verified` or tenant
> caller hits the `own` path.
>
> Verified live on 2026-09-01: setting `owner_field: "kolom_bestaat_niet"` on a governed sandbox entity
> returned **all three rows to an owner, with no error**. The same policy would have broken for every
> member.
>
> So an owner test proves only that the entity is governed — never that the scoping is correct. Verify
> every policy from an actual member or visitor session before trusting it.

## Worked examples

Per-user "My Account" (each verified visitor sees/edits only their own row) — live on Lenshouse:

```json
{ "owner_field": "email",
  "rules": {
    "read":   {"verified":"own","project":"all"},
    "list":   {"verified":"own","project":"all"},
    "update": {"verified":"own","project":"all"},
    "create": {"project":"all"},
    "delete": {"project":"all"} },
  "fields": {"verified": {"hide": ["<admin-only columns>"]}} }
```

Org-wide tenant isolation (all users of a tenant share the org's rows; SAPI/`wst_` sessions only):

```json
{ "owner_field": "tenant_code",
  "owner_scope": "tenant",
  "rules": {
    "read":   {"verified":"own","project":"all"},
    "list":   {"verified":"own","project":"all"},
    "update": {"verified":"own","project":"all"},
    "create": {"verified":"self","project":"all"},
    "delete": {"verified":"deny","project":"all"} } }
```

`create:self` auto-stamps `tenant_code` for the creating tenant; a cross-tenant row → 403; a
non-tenant/visitor caller → 404. Verified 12/12 at the gate level (#942, 2026-07-29).

**Reachable from a browser via the `records` bridge (#1310)** — read the delivery-path section
above for its verification status before you build on this example.

## Triaging a data-access capability request

Most "we need a feature for X" requests about member data are **configuration, not code**.
Two worked cases: 24451 (`update-entity-policy-json`) turned out to be a live feature the
requester could not find, and 27203 asked for a new match mode that already existed under
another name (`owner_scope`). In both, the platform answer was a policy plus a pointer.

Before scoping any build, separate the two questions:

- **"Which rows may this person see?"** → `policy_json`. Almost always configuration.
  `account.sources` covers per-user reads across arbitrary entities; `records` +
  `owner_scope: "tenant"` covers org-wide sharing; `gated-files` covers files.
- **"Can any service reach this data under the caller's identity at all?"** → that is the
  question worth building for. Non-owner identity exists only on the SAPI execute route,
  so a genuine gap is always a **missing route**, never a missing feature. #1310 is the
  example: one read verb on a route that already carried the identity.

If a request does not survive the first question, answer it with configuration and close
it — and check whether the customer-facing skill made it findable, because a request that
should have been self-service is usually a documentation failure rather than a user error.

## Negative cross-tenant test (required for policy changes)

Per the IDOR rule in the dev skill, add a gate-level test to `websitepublisher-tests` when touching
authz: construct `CallerIdentity::tenant('A', …)` / `visitor` / `owner`, call the `MapiPolicyGate`
methods, and assert scoping (tenant A sees only A, cross-tenant → 403, visitor/unknown → 404, create
self-stamp, owner → all). Pure logic — no HTTP/DB needed.

---

*Companion reference to the WebsitePublisher.ai dev skill.*
*Last updated: 22 september 2026*
