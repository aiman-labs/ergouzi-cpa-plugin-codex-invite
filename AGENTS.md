# AGENTS.md — Ergouzi Codex Invite Plugin

Fork: `aiman-labs/ergouzi-cpa-plugin-codex-invite`; upstream:
`LTbinglingfeng/cpa-plugin-codex-invite`. Builds the `codex-invite` dynamic-library archive consumed
by `ergouzi-CLIProxyAPI`.

## Workflow

| Task | Skill, when available |
|---|---|
| Refresh workspace context | `ergouzi:workspace-context-refresh` |
| Upstream sync and baseline decisions | `ergouzi:fork-upstream-sync` |
| Release, deploy, rollback and receipts | `ergouzi:release-manager` |

For authorized Codex PR work, use `ergouzi:github-pr-review-loop` when available. Close only after
latest-head review completes and triage leaves no confirmed P0/P1; do not chase an emoji. There is
no fixed round cap: continue with substantive progress, stop and hand off
repetition/oscillation/stalls, and keep unresolved confirmed P0/P1 blocking.

- Release tags use `vX.Y.Z-ergouzi.N` from upstream releases/tags; record the upstream commit in
  `docs/upstream-sync/` when no release/tag exists.
- Code checks: `make test`, `make vet`, `make build`; `make package` creates release artifacts. CGO
  and target `GOOS`/`GOARCH` must match the host before declaring an artifact release-ready.
  Docs-only changes need content/link/diff checks.

## Boundaries

- Simplified Chinese responses; English code/comments.
- Server calls use the internal CPA Management API when browser-only gates such as Cloudflare Access
  protect production.
- Do not persist ChatGPT tokens, management keys, proxy URLs, cookies or invite secrets. Do not
  commit archives/checksums/build outputs without explicit artifact-publication scope.

## Authorization

- Preserve unrelated changes. Branch/worktree creation and commits each require explicit user
  authorization; push, PRs, comments, merge, release and deployment require their own scoped
  authorization.
- Show the exact file scope before committing. Do not add AI signatures, generated-by markers or AI
  co-author trailers; do not rewrite history without explicit authorization.

## Feature Development and Protection

- For feature work, use `ergouzi:feature-workflow` when available; read `docs/features/README.md`
  and `docs/features/registry.json`.
- New behavior uses an authorized `feature/<topic>` branch; bounded regressions may use
  `fix/<topic>` and reuse the feature ID. Update affected records with invariants, focused paths,
  regression entrypoints, upstream provenance/strategy and cross-repository contracts. Inherited
  upstream behavior is not original downstream work.
- Before a PR, read `.github/PULL_REQUEST_TEMPLATE.md` and run
  `node .github/scripts/feature-contract.mjs --repo . --base <intended-base>`. Pure docs changes
  need no new feature ID; the checker does not replace semantic review or tests.
- Sync uses an authorized `sync/upstream-<version>` from verified product main, never direct
  integration on main. Read `docs/upstream-sync/{README.md,conflict-decisions.md,sync-history.md}`
  and any protection matrix; analyze the explicit upstream baseline-to-target delta against the
  registry, then update DEC/matrix/history with actual results and limitations.
- Feature implementation, release and deployment are separate facts. This guidance grants no Git,
  GitHub, release or production permissions.

## Code Review Rules

- Report only high-confidence P0/P1 defects introduced or materially worsened by this PR: critical
  outages or serious reachable correctness, security, billing, data-integrity or production
  regressions.
- Identify the concrete trigger, affected code path and serious consequence, supported by code,
  focused tests or runtime evidence. Do not invent environmental assumptions; an executable exploit
  is unnecessary when the code proves the failure.
- Exclude P2/P3, style, routine refactors, speculative hardening, unrelated old defects, and missing
  tests/docs or convention violations alone. Return no findings when nothing meets the threshold.
- Required CI, tests, human approvals and merge authorization remain separate requirements.
