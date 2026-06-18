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
- Check the latest upstream plugin release tag at both the start of a sync and
  again immediately before merging the PR.
- Distinguish upstream plugin feature syncs from Ergouzi CPA SDK compatibility
  rebuilds. If upstream plugin has no new release, document the CPA SDK target
  as the reason for the rebuild instead of calling it an upstream feature sync.
- Treat automated PR review as advisory. Fix comments that identify a real
  regression in invite flow, CPA plugin ABI compatibility, or code just changed
  by the PR. Do not keep expanding a sync for unrelated surfaces; record those
  as deferred/ignored when needed.
- After a sync PR is merged, update local `main` from `origin/main` and verify
  that the local working tree is clean and `HEAD` equals `origin/main`.

## Normal Sync Checklist

1. Fetch `origin` and `upstream`.
2. Check worktree cleanliness.
3. Identify the latest upstream plugin release tag and the CPA SDK version this
   plugin should build against.
4. Count merge base, upstream commits, Ergouzi commits, and touched files.
5. Resolve conflicts by behavior, not by blindly choosing one side.
6. Add or update DEC entries for real tradeoffs.
7. Update `sync-history.md` with snapshot and verification.
8. Before merging the PR, re-check the latest upstream plugin release tag.
9. After merging, fast-forward local `main` to `origin/main`.
