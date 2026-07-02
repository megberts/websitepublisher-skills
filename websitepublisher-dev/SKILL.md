---
name: websitepublisher-dev
description: >
  Platform development skill for WebsitePublisher.ai internal sessions.
  Use this when working on the platform itself — not for building customer websites.
  Covers TAPI task tracking, internal conventions, and development workflow.
license: MIT
metadata:
  author: websitepublisher-ai
  version: "1.2"
  website: https://www.websitepublisher.ai
  docs: https://www.websitepublisher.ai/docs
  mcp: https://mcp.websitepublisher.ai
---

# WebsitePublisher.ai — Platform Dev Skill

> This skill is for internal platform development sessions only.
> For building customer websites, use: https://www.websitepublisher.ai/skills/websitepublisher-api/SKILL.md

---

## Session Start — Required Steps

Every development session MUST start with these two steps before doing anything else:

**Step 1 — Load platform skill**
Read the customer-facing skill for platform conventions:
```
https://www.websitepublisher.ai/skills/websitepublisher-api/SKILL.md
```

**Step 2 — Load open tasks**
Call `list_tasks` to get the current open backlog:
```
list_tasks(scope="global")
```

Then call `get_task_history` on the `session-start-checklist` platform task for the latest instructions:
```
get_task_history(slug="session-start-checklist")
```

Do not start any work until you have read both.

---

## TAPI — Task Tracking (Required Workflow)

TAPI is the append-only task tracker for all platform development work.
Every session that makes progress on a task MUST update it via `add_task_history`.

### Tools

| Tool | When to use |
|------|-------------|
| `list_tasks` | Session start — get open backlog |
| `get_task` | Before working on an item — check current state |
| `get_task_history` | Before working — read what other sessions did |
| `create_task` | New work item that doesn't exist yet |
| `add_task_history` | After completing work, hitting a blocker, or making a decision |

### History types

| Type | Use for |
|------|---------|
| `progress` | Reporting completion percentage + status update |
| `note` | Short observation or decision, no MD content |
| `snippet` | Handover content, session summary, debug findings |
| `blocker` | Something blocking progress — always include what's blocking and why |
| `architecture` | Architecture document, include `version` field (v1.0, v1.1, ...) |
| `fork` | Reference to a sub-task that was created |

### Workflow per session

1. `list_tasks` → pick up open items
2. `get_task_history(slug="...")` → read what was done before
3. Do the work
4. `add_task_history` — type=progress, actual completion_pct, honest status
5. If a handover is needed: `add_task_history` with type=snippet, content = full MD

### Completion percentage rules

- `completion_pct` is only set on `type=progress` records
- Current completion = MAX ever reported — going back down requires explicit new record
- Be honest: 40% means 40%. Not "almost there" = 80% when DB isn't touched yet.
- `status=done` + `completion_pct=100` only when deployed and verified

### Session label convention

Always set `session_label` so history is attributable:
- `"claude-desktop"` — Claude Desktop MCP session
- `"sessie-a"`, `"sessie-b"` — parallel sessions
- `"browser"` — claude.ai browser session

---

## Platform Conventions

These apply to all code written for WebsitePublisher. Read before touching any file.

### Code

- **No regex on large HTML files** — use strpos/substr. Prior bad experience with regex on large files causing corruption.
- **Surgical changes preferred** — minimal targeted patches over broad rewrites

### Deployment

- Test environment available at `*.test.websitepublisher.ai`
- Server details are managed internally — not stored in this skill

### API conventions

- `patch_page` over `update_page` for small changes — saves tokens, preserves history
- `patch_page` requires current `base_version_hash` from `get_page` first
- MAPI plural endpoint: `/showcases` not `/showcase`
- `configure_form` requires all fields on every call — no partial updates
- `api_project_id` in responses (not `websumo_project_id`)

### PAPI SEO

- PAPI SEO values always override AI-generated HTML content
- `seotitle` (browser/search title) ≠ `title` (WebSumo builder menu label) — distinct DB fields
- Always include `<!-- Optimizer - ... -->` comment tags in every page
- Auto-injected by createPage/updatePage — do not strip them

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

## Infrastructure Reference

| Resource | Value                        |
|----------|------------------------------|
| Production | Clustered                    |
| Test env | `*.test.websitepublisher.ai` |
| CDN | `cdn.websitepublisher.ai`    |
| MCP server | `mcp.websitepublisher.ai`    |
| API gateway | `api.websitepublisher.ai`    |

### API layers

| Layer | Prefix | Purpose |
|-------|--------|---------|
| PAPI | /papi/ | Pages & Assets |
| MAPI | /mapi/ | Entities & Records |
| SAPI | /sapi/ | Sessions & Forms |
| VAPI | /vapi/ | Vault / Credentials |
| IAPI | /iapi/ | Integrations (proxy) |
| DAPI | /dapi/ | Dashboard |
| WAPI | /wapi/ | WebSumo wrapper |
| AAPI | /aapi/ | Agent API (scheduled tasks) |
| CAPI | /capi/ | Coach API (website intake) |
| TAPI | /tapi/ | Task API (this system) |
| MCP  | /mcp   | Model Context Protocol server |

---

## MCP / TAPI Curl Testing

Always include all three headers:
```bash
curl -s -X POST "https://api.websitepublisher.ai/tapi/tasks" \
  -H "Authorization: Bearer ${TOKEN}" \
  -H "Content-Type: application/json" \
  -H "Accept: application/json" \
  -d '{...}'
```

---

*Dev Skill version: 1.2*
*Last updated: 29 juni 2026*
