# Conflict Decisions

This file records final Ergouzi decisions for `cpa-plugin-codex-invite`
upstream sync.

Status values:

| Status | Meaning |
|---|---|
| `decided` | Final unless a future upstream change directly conflicts |
| `review` | Needs user or second-reviewer confirmation |
| `deferred` | Intentionally postponed |

## DEC-20260616-001: Maintain Codex Invite as an Ergouzi production fork

| Field | Value |
|---|---|
| Status | `decided` |
| Area | workflow |
| Upstream base | `7291a5d` |
| Ergouzi source | `main` |

Final decision: Ergouzi uses
`aiman-labs/ergouzi-cpa-plugin-codex-invite` as the production Codex Invite
plugin fork. Changes are made directly on `main`; upstream is synced
periodically but is not a deployment source.

Review notes: Future sync work should preserve this repository identity and
avoid replacing production with upstream release assets.

## DEC-20260616-002: Use server-side internal Management API origin

| Field | Value |
|---|---|
| Status | `decided` |
| Area | management-api |
| Upstream base | `7291a5d` |
| Ergouzi source | `main` |

Final decision: Add `management_origin` to plugin config and prefer it over
browser-provided origins when the plugin calls CPA Management API endpoints.
Production should set this to the local CPA service, such as
`http://127.0.0.1:8317`.

Review notes: This avoids Cloudflare Access / Tunnel loops where the plugin
server calls `https://cpa.ergouzi.life/...` without the browser's Access
session. During upstream sync, do not remove this behavior unless CPA exposes a
first-class local host callback for authenticated Management API calls.
