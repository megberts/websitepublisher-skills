---
name: websitepublisher-dev
description: >
  Platform development skill for WebsitePublisher.ai internal sessions.
  Use this when working on the platform itself — not for building customer websites.
  Covers how work is verified here, TAPI task tracking, deployment reality across
  both nodes, the code reader, the integration guidance store, and platform conventions.
license: MIT
metadata:
  author: websitepublisher-ai
  version: "2.1"
  website: https://www.websitepublisher.ai
  docs: https://www.websitepublisher.ai/docs
  mcp: https://mcp.websitepublisher.ai
---

# WebsitePublisher.ai — Platform Dev Skill

> This skill is for internal platform development sessions only.
> For building customer websites, use: https://www.websitepublisher.ai/skills/websitepublisher-api/SKILL.md

### Size budget — 28.000 bytes

Every session loads this file in full, so its length is a running cost. **Past 28.000
bytes, move reference material out to a companion document and link it — do not trim the
rules to make room.** Measure with `wc -c`.

A **rule** changes what you do next and belongs here. A **reference** is something you look
up while writing one specific thing and belongs in its own file.

> It moved twice on the day it was set (24 → 26 → 28) because it was set mid-repair, and a
> budget fixed while the file still is measures a draft. **Do not move it again to fit an
> addition** — cut reference, or write a companion.

### Companion documents

| Document | Covers |
|---|---|
| `skills/websitepublisher-dev/authz-policy-json.md` | `policy_json` grammar, worked examples, delivery paths, verification log |

Served at `https://www.websitepublisher.ai/<path>`.

> **`get_skill` cannot deliver these.** Its enum is `main` / `design` / `dev` only, so a
> session without web access cannot reach a companion at all. If a fetch fails, say so and
> work from what the skill itself states — do not guess the grammar. Same for
> `integrations.txt`: when the URL is unreachable, `search_integrations` and
> `list_integrations` give you the same answers over MCP.

---

## Session Start — Required Steps

Every development session MUST start with these two steps before doing anything else:

**Step 1 — Load platform skill**
```
get_skill(skill_name: "main")
```
Use the tool, not the URL. `get_skill` serves the current sectioned version with an index;
the raw URL serves the whole file and can lag behind by several minor versions.

**Step 2 — Load open tasks and the current checklist**
```
tasks(operation: "list", scope: "global")
tasks(operation: "history", slug: "session-start-checklist")
```

Do not start any work until you have read both.

---

## How We Work Here

This section is the point of the skill. The conventions further down say *what* the
platform is; this says *how* to touch it without breaking it or misreporting it.

### Verify, don't infer

Reading a flag is not the same as walking the path. A guidance entry was published claiming
`execute_integration` could not reach the browser endpoints of `data_grid`, inferred from
`mcp_tool: false`. That flag hides an endpoint from `tools/list`; it does not block
execution. The entry was wrong and had to be republished.

Before stating how something behaves, follow the code path that implements it. If you have
not followed it, say so in the same sentence: *"the escaping bug is certain; the exploit
chain via public forms is plausible and unverified."* A qualified claim is useful. An
unqualified guess that turns out wrong costs more than the time it saved.

This covers identifiers too: a ticket number written into a code comment before the ticket
exists is a guess. Create it first.

### Search for a precedent before you write "cannot"

Following a code path tells you what **that** mechanism does. It does not tell you the limit
of the system, and mistaking the two is the most expensive error in this file.

On 22 September, "SSR cannot render integration output" was written down as fact after
reading `MapiSsrInjector`. `CatalogSsrResolver` — three files away in the same directory —
does exactly that, has done since TAPI #802, and `resolveRecords()` dispatches on it in
eight lines. The same day produced two more of the same shape: `mcp_tool` above, and
`code_search`'s `path` below. Three impossibility claims, three existing counter-examples.

**Before writing that something is not possible, grep for a precedent.** One search in the
same directory would have caught all three. And when you do write it, write what you
checked: *"no path exists that I found"* is honest; *"no path exists"* is a claim about the
whole system from one file.

### The measurement can be the bug

Three conclusions in one day were wrong because the *check* was wrong, not the code:

- `code_search` with a **file** path in `path` returns 404 "niet gevonden" even when the
  pattern is in that file. Conclusion drawn: "there is no `confirm()` here." There was.
- `tail -c 120` was used to look for `[keywords]` in `integrations.txt`, which carries them
  in field 4, not at the end of the line. The check could not have succeeded.
- `grep -c 'self::CONST'` was expected to return 6 — five uses plus the definition. The
  definition reads `private const CONST`, without `self::`. Five was correct.

When a result contradicts what you expect, **suspect the instrument before the code**.
State what a passing check would have to look like, then check that your command can
actually produce it.

The same reasoning separates a fatal from an outage. When one tool on a server answers
normally and another returns 502, the gateway is fine and the fault is inside that one
method. A 502 is a symptom, not a diagnosis.

### Two nodes, every time

Production is **h17** (`hosting17.m25.nl`) and **h19** (`hosting19.m25.nl`). They share
**one database** but have **separate filesystems**. Code, manifests, skills and commands
exist twice.

Every deploy verification runs against both. A half-deploy — new files on one node, patches
missing on the other — has happened more than once and is invisible from a single check.

Do not use the version string in a GAPI response as proof of a deploy. It is a daemon
version: it has sat unchanged since 9 September while PHP was deployed many times. The
proof is reading the changed lines back, on both nodes.

### A report you learn to ignore is worse than no report

If a check routinely shows lines that turn out to be fine, people stop reading it — and
then they miss the one time it is right. That is why the `guidance_hint` in error responses
is deliberately **not** attached to 429s: a rate limit is not a usage mistake, and a hint
that appears on every error teaches the caller to skip the field.

**Before adding a line to any report, estimate how often it will be actionable. Under half
the time: leave it out, or give it its own category so the reader can skip it as a group.**

### Structure beats checks

A rule that has to be enforced can be forgotten. A structure that makes the mistake
impossible cannot. Guidance history lives in its own directory (`guidance-history/`) rather
than inside the current export, so `--prune` on the current state physically cannot reach
it. The guard that was designed for that in v0.2 became unnecessary and was never built.

**When you are about to add a validation, a guard or a "don't forget" comment, first try to
change the layout so the mistake cannot be expressed.** If you cannot, keep the check and
write down why the structural fix was not possible.

### Tests need teeth

A green test that cannot fail proves nothing. When fixing a defect, make the harness
reproduce the **old** behaviour and assert that it breaks. The `escAttr` harness carries a
faithful reimplementation of the old escaper and requires it to fail on exactly the payloads
that mattered — otherwise the suite would pass just as happily against the broken code.

Determinism is the strongest available proof. Running `guidance:export` twice and getting
*0 changed* says more than any inspection. Same for the cross-node check: if h19 pushes
nothing half an hour after h17, nothing drifted.

Assert on the **set of names**, never on a count. Counts move with normal work: a test plan
expected 88 entries where 87 was correct, and a suite asserted "55+ tools" while live is 45.
Both are tripwires, not assertions.

### Surgical edits — the anchored-patch recipe

Long single-line strings (tool descriptions, server instructions) cannot be safely rewritten
from memory, and the code reader truncates them so you cannot read them back in full. Patch
them by anchor instead:

```bash
cd /home/websumo.com && cp <file> /tmp/$(basename <file>).bak.$(date +%s)

# 1. prove the anchor is unique — if this is not 1, STOP and lengthen the anchor
grep -cF "<anchor>" <file>

# 2. apply; \Q…\E makes brackets and dots literal
perl -0pi -e 's/\Q<anchor>\E/<replacement>/' <file>

# 3. syntax + a count that can only be right one way
php -l <file>
grep -n '<marker>' <file>
```

Step 1 is not ceremony: an anchor ending `without passing through the AI conversation.',`
matched **two** tool descriptions, and the count check is the only thing that caught it
before the vault tool received a pointer it has no business carrying.

Never re-type a file's contents from earlier tool output: it may have been truncated.

**`php -l` proves syntax, not resolution.** A call to a class this file has never imported —
`Cache::remember()` in a file whose `use` block holds only `Log` — resolves against the
current namespace, passes `php -l` cleanly, and fatals on the first request. That took
`get_started` down for every connecting MCP session, while the patch notes carried the
`use`-block reminder for the *other* file in the same change. Adding a call to a class you
have not used in this file means reading the `use` block first.

### Say what you changed, in the caller's terms

When you change something the user cannot see in the conversation — published guidance, a
manifest, a cron — name it explicitly and name the slug. "Live, revision 4" reads like you
are presenting code. *"I updated the guidance entry `data_grid/grid-list-vs-list` to v3;
here is what was wrong"* does not.

**Every reply that changed invisible state opens with one line naming what changed and
where.** Not at the end, not implied by a tool call the user has to interpret.

### Retract out loud

A finding that turns out to be wrong gets a written retraction in the same ticket, with the
reason. `revert_of: null` was reported as a cosmetic defect; it is a meaningful value
(reverted from an empty delivery) and the report was made without reading the write path.
Left silent, that finding sits in the backlog until someone "fixes" it.

The same applies to your own verification methods. Correcting them in writing is what stops
the next session from repeating the mistake.

---

## TAPI — Task Tracking (Required Workflow)

TAPI is the append-only task tracker for all platform development work. Every session that
makes progress MUST record it.

### One tool, one operation parameter

`tasks(operation: …)` is the single entry point. The older granular tool names
(`list_tasks`, `get_task`, `add_task_history`, …) **no longer exist**.

| Operation | Use |
|---|---|
| `list` | Session start — open backlog. Roots only unless `parent_id_null: false`. |
| `get` | Before working on an item — current state + architecture versions. |
| `history` | Before working — what other sessions did. `with_content: true` fetches the MD from S3. Filter with `type`. |
| `search` | 1–3 distinctive words, AND-matched across title/description/slug and history summaries. |
| `create` | **Requires both `slug` and `title`.** Optional: `description`, `parent_id`, `related_to`, `tags`, `scope`. |
| `add_history` | After work, a blocker, or a decision. Requires `type` + `summary`. |
| `update` | Metadata only (title/description/parent_id/related_to/tags). Status and completion stay history-driven. |
| `export` | Markdown overview grouped by status. |

Two traps worth knowing:

- `id` is the **task number** (#1437). `parent_id` is the **internal id** from the `id`
  field in responses — a different number. Mixing them up silently re-parents nothing.
- `delete` is refused while a task has children, and is a soft delete.

### History types

| Type | Use for |
|---|---|
| `progress` | Completion percentage + status |
| `note` | Short observation or decision, no MD content |
| `snippet` | Handover content, session summary, debug findings |
| `blocker` | Include what blocks and why |
| `architecture` | Architecture document, with `version` (v0.1, v1.0, …) |
| `fork` | Reference to a sub-task that was created |

### What belongs in TAPI — and what does not

**Source code does not go in TAPI.** It lives in git and on the servers. TAPI holds
**architecture and decision documents**: what the path was, what was considered, what was
rejected and why. Those land in S3 as MD content and are what a future session actually
needs.

A record that says *"we chose two directories so that `--prune` cannot reach the history"*
is worth keeping. A record containing 700 lines of PHP is not.

### Completion percentage

- Only set on `type=progress`.
- Current completion = MAX ever reported; going down needs an explicit new record.
- Be honest: 40% means 40%. Not "almost there" = 80% when the DB is untouched.
- `status=done` + `completion_pct=100` only when **deployed and verified on both nodes**.

### Session label

Always set `session_label` so history is attributable: `"claude-desktop"`, `"browser"`,
`"sessie-a"`, or a dated topic label such as `"2026-09-22-datagrid-snippet"`.

---

## Deployment Reality

### The sequence

1. Replace the file(s) on **both** nodes.
2. `php -l` on every changed PHP file.
3. `optimize:clear`, fpm reload, `queue:restart`.
4. Read the changed lines back through the code reader — on h17 **and** h19.

### What needs a fresh client session (#1293)

`tools/list` is fetched **once per client session**. Change a tool name, description or
schema and a running session will not see it — refreshing inside that session does nothing,
and reconnecting is not reliable either. The same holds for the MCP server instructions.

This has cost hours more than once, including three separate times on 22 September. It is
not opcache. It is the session boundary.

| Change | Visible when |
|---|---|
| Tool description / schema / name | New client session |
| MCP server instructions | New client session |
| Tool *result* content (e.g. `get_started` rules) | Immediately |
| Helper / controller logic | Immediately after fpm reload |

When you deploy a tool-definition change, say plainly that it will only be visible in a new
session, and verify the **code** on both nodes instead of claiming the change is live.

`tools/list` over the legacy Bearer path returns the descriptions the server is serving right
now — the only way to check a deployed description from inside a running session.

---

### Scheduled work

Crons run from the OS crontab, not Laravel's scheduler — `Kernel::schedule()` is empty and
`commands()` only requires `routes/console.php`. A new artisan command **must** be
registered in `$commands`; there is no `$this->load()` autodiscovery.

Jobs that run on both nodes are staggered rather than simultaneous, so that a difference
between the nodes shows up in the logs instead of racing.

---

## The Code Reader (GAPI)

Read-only access to the platform source and logs on either node: `code_search`, `code_read`,
`code_tree`. Scope is `app/`, `routes/`, `resources/views/`, `storage/logs/`.

Enabled per session with `gapi on <hours>`. **The same capability gates
`integration_guidance`** — if the guidance tool refuses with a code-reader message, that is
why, and no reconnect is needed after enabling it.

### Limits and traps

| Behaviour | Consequence |
|---|---|
| `path` must be a **directory** | A file path returns 404 "niet gevonden" regardless of content (#1473). Use the containing directory. |
| Search is **case-sensitive** by default | `bak` does not match `Bakkerij`. Pass `case_sensitive: false` when in doubt. |
| Long lines are truncated | `…[afgekapt]`. You cannot read a long tool description in full; patch it by anchor instead. |
| Quota 50 calls/day | Resets at midnight. Batch independent searches; do not re-run a search to "check". |
| Rate limit ~11/min | Spread bursts. |
| The `version` field is a daemon version | It is **not** a deploy marker. It has been static since 9 September. |

Secrets are masked before a result leaves the node. A line showing `***REDACTED***` is real
source with the value removed — and the redactor masks **its own patterns**, so a masked
line is not evidence that a key is stored there. Never copy such a line into a file as if it
were the source.

---

## Integration Guidance Store

Integration-specific instruction (how-to, page contracts, anti-patterns, pitfalls, snippets)
lives in the database, not in a growing skill. It is delivered to every AI on the platform
through `get_integration_schema`.

- Tables `papi_integration_guidance` + `papi_integration_guidance_state`, **append-only**:
  changing an entry writes a new version and never overwrites one. Redis-cached per service
  revision.
- Owner-only tool `integration_guidance` with `list`, `get`, `preview`, `propose`,
  `publish`, `revert`, `retire`. `publish`, `revert` and `retire` require `confirm: true`;
  `propose` requires `evidence` naming an owner source.
- **Nothing is ever truncated.** Limits are enforced by refusing. Apply the same rule to
  anything you build on top of it.
- A failed `execute_integration` carries a `guidance_hint` pointing at the entries that
  plausibly apply (#1464).

### Backup

`guidance:export --history --s3` writes one file per entry and pushes to the private
bucket, twice daily from both nodes, staggered. `guidance:import --dry-run` compares an
export against the database and **never writes**: a second write path would bypass the
validation `integration_guidance` enforces. Details in TAPI #1463 / #1470.

### When you change guidance

Say so, in plain words, and name the slug. Publishing a correction is a normal move and does
not need permission — but presenting it without saying "I updated the guidance" reads as if
you are showing code the user cannot find.

---

## Platform Conventions

### Code

- **No regex on large HTML files** — use `strpos`/`substr`. Prior corruption incident.
- **Surgical changes preferred** — minimal targeted patches over broad rewrites. See the
  anchored-patch recipe above.
- **Escaping is positional.** An escaper that is safe for element content is not safe for
  attribute values: serialising a text node escapes `&`, `<`, `>` but not the quote. Use a
  dedicated attribute escaper between quotation marks. This was a live stored-XSS in the
  generated `data_grid` snippet (#1471).
- **JSON embedded in a `<script>` block** needs `JSON_HEX_TAG | JSON_HEX_AMP |
  JSON_HEX_APOS | JSON_HEX_QUOT`, otherwise a value containing `</script>` closes the block.

### API conventions

- Test environment at `*.test.websitepublisher.ai`
- `patch_page` over `update_page` for small changes — saves tokens, preserves history
- `patch_page` requires the current `base_version_hash` from `get_page` first
- MAPI plural endpoint: `/showcases` not `/showcase`
- `configure_form` requires all fields on every call — no partial updates
- `api_project_id` in responses (not `websumo_project_id`)

### Public integration catalog

`https://www.websitepublisher.ai/integrations.txt` is the public, auth-less catalog of
every integration the platform exposes, rendered live from
`IntegrationRegistry::getCatalog()` through the `IntegrationVisibility` decision point
(#1403) — so `system` and `private` manifests never appear in it.

What this means when you touch a manifest: it is public the moment it is not `system` or
`private` — no publish step, 300 s cache — and the description is read worldwide, so keep it
English. `search_keywords` folds into the line and feeds `search_integrations` scoring.

Pointers live in the MCP server instructions, `get_started`'s rules and five tool
descriptions — all built from `McpController::INTEGRATIONS_CATALOG_URL`. **Change the URL
there and nowhere else** (#1447).

### PAPI SEO

- PAPI SEO values always override AI-generated HTML content
- `seotitle` (browser/search title) ≠ `title` (WebSumo builder menu label) — distinct DB fields
- `<!-- Optimizer - ... -->` tags are auto-injected by createPage/updatePage — never strip them

---

## Security — Tenant Isolation (IDOR)

> The #1 source of repeat audit rounds. Every endpoint is multi-tenant by default.
> Build with this in mind from the first line — it is far cheaper than catching it in review.

**IDOR = Insecure Direct Object Reference.** The endpoint accepts a direct reference to an object (an ID in the URL or body) and forgets to check that the caller is *allowed* to touch that object. Authorisation is not authentication.

### The rule — every endpoint that accepts an object reference

Any endpoint that takes an object ID (`record_id`, `line_id`, `order_id`, `asset_id`, `entity_id`, `project_id`, …) MUST verify the object belongs to the **resolved** `website_id` *before* any read or write.

- **"Authenticated" ≠ "authorised".** A valid session/token proves *who* the caller is, not *what they may touch*.
- **Lookup is always scoped.** Always `WHERE website_id = :resolved_website_id`. Never look up by `id` alone and then trust the result.
- **Scope against the resolver's `website_id`, never the raw `project_id`/`api_project_id` from the request.** The request can lie; the resolver cannot. Use the engine's resolved `website_id` throughout — not the dashproject ID from the body/URL.
- **Sanitise every response.** On any response that can echo stored data, run the response sanitiser (VaultSanitizer / equivalent) so no secret or cross-tenant field leaks.
- **Foreign-tenant ID → 404, not 403.** A 403 confirms the object exists; a 404 leaks nothing about another tenant's data.
- **In doubt: scope tighter.**

### Two failure modes to watch

- **Horizontal** — same privilege level, other tenant (`order/1001` → `order/1002`). Caught by the `website_id` scope.
- **Vertical** — a user-level endpoint hands back an admin-level object. Ownership alone does not catch this; also check role/capability.

### What actually stops the regression — the test, not the rule

A skill rule reminds; a test enforces. For every new or changed endpoint that accepts an ID, add a **negative cross-tenant test** to `websitepublisher-tests`:

> Request the object with a **foreign-tenant** ID → assert **404** (or 403 for the vertical case).

This runs before deploy. Without it, "remember IDOR" gets forgotten exactly the way it is forgotten now — and you are back in audit round 5.

---

## AuthZ Governance — `policy_json` (#942)

Row-level entity access control is a **declarative `policy_json` block per entity**, enforced
at the unbypassable data-layer chokepoint (`MapiEntityHelper` → `MapiPolicyGate` →
`AccessPolicy`). Set it with `entities(operation: "update", entity_name: …, policy_json: …)`.

**Opt-in per entity.** No `policy_json` means ungoverned — the gate is a byte-identical
pass-through and `public_read` still controls anonymous visibility. Once an entity carries
*any* policy, `public_read` is **ignored entirely**.

> **Full grammar, worked examples, delivery paths and the verification log:**
> `https://www.websitepublisher.ai/skills/websitepublisher-dev/authz-policy-json.md`
>
> Read it before writing a policy. What follows here is only what you need to *know*
> without looking anything up.

### The four things that bite

**1. Testing as owner does not exercise the policy at all.**
An owner runs at tier `project` with grant `all`, and `all` performs no ownership matching —
so `owner_field` is never read. Verified live on 2026-09-01: `owner_field:
"kolom_bestaat_niet"` on a governed sandbox entity returned **all three rows to an owner,
with no error**, and would have failed closed (HTTP 500) for every member. An owner test
proves only that the entity is governed, never that the scoping is correct. **Verify every
policy from an actual member or visitor session.**

**2. The server validates JSON shape, not rule correctness.**
A wrong `owner_field` or an over-permissive rule is accepted without complaint. Always
activate on a sandbox project first, never straight onto a live table.

**3. SSR renders governed entities without complaining — into a shared cache.**
`MapiSsrInjector` deliberately does **not** check `public_read` (r.732, r.760-765,
verified on h17 22-09): SSR runs inside the site on the owner's own template, and routing
it through the public resolver forced owners to open `/mapi/public` just to display their
own data. So a governed entity renders fine.

What did not change is the cache key — `mapi:ssr:{website_id}:{entity}` (r.740), with **no
session dimension**. One cache serves every visitor of the page. SSR therefore still cannot
carry anything that differs per person or per tenant, and since the `public_read` check no
longer stops you, **nothing does**. This is a rule you enforce, not one the platform
enforces for you. Gated content is fetched client-side from a verified session.

An empty `wps-mapi` block now means the entity name does not resolve — it no longer implies
`public_read: false` (supersedes the earlier #1311 note).

**4. Refusals arrive as HTTP 200.**
Only what the controller itself rejects (no session, missing CSRF) returns a real status.
Everything the integration refuses — 403, 404, 422, 409 — comes back as **200** with
`success: false` and an `upstream_status`. Branching on `res.ok` turns a permission denial
into a silent empty render. **Check `success`.**

### 404 or 403 — this is not a contradiction

The IDOR rule above says a foreign-tenant ID returns **404**. The gate returns **403** on an
ownership mismatch. Both are right, because they answer different questions:

- **No legitimate relationship to the container at all** → **404**. The caller has no grant
  on this entity, or the object belongs to another tenant entirely. A 403 here would confirm
  that the object exists, which is the leak the IDOR rule closes.
- **A legitimate grant on the container, but not on this row** → **403**. A verified member
  listing a governed entity already knows it exists; they hold a grant on it. Telling them
  "not yours" reveals nothing they did not already have.

The test: *could this caller legitimately have known the container exists?* No → 404.
Yes → 403.

Full contract: `deny` → 404 · `own`-mismatch → 403 · state guard failed → 409.

### Negative cross-tenant test (required for policy changes)

Per the IDOR rule above, every authz change gets a gate-level test in
`websitepublisher-tests` asserting that a foreign tenant sees nothing. Pure logic — no HTTP
or DB. The constructor calls and the assertions to make are in the companion.

---

## Infrastructure Reference

Production is clustered: **h17** (`hosting17.m25.nl`) and **h19** (`hosting19.m25.nl`),
one shared database, separate filesystems. Test env `*.test.websitepublisher.ai`,
CDN `cdn.websitepublisher.ai`, MCP `mcp.websitepublisher.ai`,
API gateway `api.websitepublisher.ai`.

### API layers

`/papi/` pages & assets · `/mapi/` entities & records · `/sapi/` sessions & forms ·
`/vapi/` vault · `/iapi/` integrations · `/tapi/` tasks · `/mcp` MCP server.
Also present, rarely touched from here: `/dapi/` dashboard, `/wapi/` WebSumo wrapper,
`/aapi/` scheduled tasks, `/capi/` intake.

---

*Dev Skill version: 2.1*
*Last updated: 22 september 2026*
