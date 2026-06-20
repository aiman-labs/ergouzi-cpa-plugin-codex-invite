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

- For upstream synchronization, baseline selection, conflict DEC records, and
  sync PR preparation, use `ergouzi:fork-upstream-sync` first.
- For plugin release, tag, package asset, CPA plugin deployment, rollback, and
  production verification, use `ergouzi:cpa-release-manager`.
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
