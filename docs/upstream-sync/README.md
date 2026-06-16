# Ergouzi Codex Invite Plugin Upstream Sync

Lightweight rules for maintaining `aiman-labs/ergouzi-cpa-plugin-codex-invite`
while periodically syncing `LTbinglingfeng/cpa-plugin-codex-invite`.

## Model

| Item | Rule |
|---|---|
| Production line | `main` in the Ergouzi fork |
| Upstream | `LTbinglingfeng/cpa-plugin-codex-invite`, fetch-only |
| Default sync style | Review and merge/cherry-pick on `main` |
| Temporary branches | Create only for large or risky conflicts |
| Decision source | `conflict-decisions.md` |
| History source | `sync-history.md` |

## Rules

- Keep Ergouzi production on our fork, not upstream release assets.
- Keep `management_origin` support unless the CPA plugin API later provides a
  safer host callback for local Management API access.
- Accept upstream invite-flow fixes when they do not reintroduce public-host
  Management API loops.
- Record only non-obvious choices as DEC entries.
- Before closing a sync, run `go test ./...`, `git diff --check`, and a
  conflict-marker scan.

## Normal Sync Checklist

1. Fetch `origin` and `upstream`.
2. Check worktree cleanliness.
3. Count merge base, upstream commits, Ergouzi commits, and touched files.
4. Resolve conflicts by behavior, not by blindly choosing one side.
5. Add or update DEC entries for real tradeoffs.
6. Update `sync-history.md` with snapshot and verification.
