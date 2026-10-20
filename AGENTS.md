# AGENTS.md — Ergouzi CPA Codex Invite Plugin

This repository is the Ergouzi fork of the CPA Codex Invite plugin. It builds a
dynamic library consumed by CLIProxyAPI.

## Repository Role

- Upstream: `LTbinglingfeng/cpa-plugin-codex-invite`
- Ergouzi fork: `aiman-labs/ergouzi-cpa-plugin-codex-invite`
- Runtime host: `ergouzi-CLIProxyAPI`
- Release artifact: platform plugin archive containing `codex-invite` dynamic
  library

## Ergouzi Workflow Skills

- When the user says `刷新 Ergouzi 工作上下文`,
  `同步二狗工作流上下文`, `Ergouzi context refresh`, or asks a thread to
  catch up with recent Ergouzi repo/workflow changes, use
  `ergouzi:workspace-context-refresh` before continuing.
- For upstream synchronization, baseline selection, conflict DEC records, and
  sync PR preparation, use `ergouzi:fork-upstream-sync` first.
- For plugin release, tag, package asset, CPA plugin deployment, rollback, and
  production verification, use `ergouzi:release-manager`.
- For GitHub PRs that rely on Codex automated review, use
  `ergouzi:github-pr-review-loop`. Treat `👀` as review in progress and `👍` or
  "Didn't find any major issues" as the accepted signal for the latest commit.
- Ergouzi plugin releases use upstream release/tag baselines with
  `vX.Y.Z-ergouzi.N` tags when an upstream version exists. If upstream has no
  release/tag, record the upstream commit baseline in `docs/upstream-sync/`.

## Commands

```bash
make test
make vet
make build
make package
```

The plugin uses CGO and platform-specific dynamic-library outputs. Verify the
target `GOOS` / `GOARCH` before treating a build artifact as release-ready.

## Working Rules

- Default response language is Simplified Chinese; code and comments use
  English.
- Keep server-side calls on the internal CPA Management API path when
  production is behind Cloudflare Access or another browser-only gate.
- Do not persist ChatGPT access tokens, CPA management keys, proxy URLs,
  cookies, or invite secrets.
- Do not commit release archives, checksums, or build outputs unless the user
  explicitly asks to publish a release artifact.
- Before committing, show the exact staged file scope.
- Do not add AI signatures, generated-by footers, or AI co-author trailers.

## Feature Development and Protection

- Use `ergouzi:feature-workflow` for new capabilities, changed business rules and
  feature provenance. Read `docs/features/README.md` and `registry.json` first.
- New behavior uses `feature/<topic>` and updates an affected stable feature
  record with invariants, focused source paths, regression entrypoints, upstream
  strategy and cross-repository contracts. Bounded fixes can use `fix/<topic>`
  and reuse the record; do not label inherited upstream behavior as downstream.
- Before a PR, run the read-only `.github/scripts/feature-contract.mjs` check
  against the intended base and fill this repository's PR template. Pure docs
  changes need no new feature ID. Checks do not replace semantic review/tests.
- Upstream synchronization uses an authorized `sync/upstream-<version>` branch
  from verified product main; never develop or integrate directly on main.
  Analyze the explicit upstream baseline-to-tag delta against the registry,
  then update DEC/matrix/history with actual results and limitations.
- Feature records describe code; release and deployment remain separate facts.
  This routing grants no branch/worktree creation, commit, push, PR, merge,
  release, deployment or production permission.
