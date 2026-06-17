# Sync History

## 2026-06-16 Fork Mechanism Baseline

| Item | Value |
|---|---|
| Ergouzi main | `main` |
| Origin | `aiman-labs/ergouzi-cpa-plugin-codex-invite` |
| Upstream | `LTbinglingfeng/cpa-plugin-codex-invite` |
| Upstream baseline | `7291a5d` |
| Upstream release | `v0.1.4` |

Changed files vs upstream:

| File | Reason |
|---|---|
| `Makefile` | Default Ergouzi plugin version suffix |
| `main.go` | Repository metadata and `management_origin` support |
| `main_test.go` | Regression coverage for internal Management API origin precedence |
| `README.md` | Deployment guidance for Cloudflare Access / tunnel environments |
| `docs/upstream-sync/*` | Ergouzi fork maintenance records |

Decisions created:

| Decision | Summary |
|---|---|
| `DEC-20260616-001` | Maintain Codex Invite as an Ergouzi production fork |
| `DEC-20260616-002` | Use server-side internal Management API origin |

Verification target before release:

```bash
go test ./...
git diff --check
rg -n '^(<<<<<<<|=======|>>>>>>>)' .
```

## 2026-06-16 CPA `v7.2.9` SDK Compatibility Rebuild

| Item | Value |
|---|---|
| Ergouzi plugin target | `v0.1.4-ergouzi.2` |
| Upstream plugin latest release | `v0.1.4` |
| Upstream plugin commits pending | `0` |
| CPA host sync target | `v7.2.9` |
| CPA SDK dependency | `github.com/router-for-me/CLIProxyAPI/v7 v7.2.9` |
| Reason | Rebuild plugin against the CPA SDK version used by the pending CPA `v7.2.9` sync. |

Findings:

- Upstream `LTbinglingfeng/cpa-plugin-codex-invite` has no new commits or
  releases after `v0.1.4`.
- This is an Ergouzi compatibility rebuild, not an upstream plugin feature sync.
- The CPA `v7.2.9` sync changes pluginhost and plugin API surfaces while keeping
  `pluginabi.SchemaVersion` at `1`; rebuilding the plugin reduces runtime drift
  before production deploy.

Verification:

```bash
make test
make vet
git diff --check
rg -n '^(<<<<<<<|=======|>>>>>>>)' .
docker run --rm --platform linux/amd64 \
  -v "$PWD":/src -w /src \
  -e GOMODCACHE=/tmp/gomod -e GOCACHE=/tmp/gocache \
  golang:1.26.4 \
  bash -lc 'export PATH=/usr/local/go/bin:$PATH; make test && make vet && make package GOOS=linux GOARCH=amd64 VERSION=0.1.4-ergouzi.2'
unzip -l dist/codex-invite_0.1.4-ergouzi.2_linux_amd64.zip
cat dist/codex-invite_0.1.4-ergouzi.2_linux_amd64.zip.sha256
```

Result:

- Local `make test` and `make vet` passed.
- Linux amd64 package passed inside `golang:1.26.4` Docker container.
- `dist/codex-invite_0.1.4-ergouzi.2_linux_amd64.zip` contains one root file:
  `codex-invite.so`.
- Package sha256:
  `733c18a0485416dae32783f2382d54ccc06a14a84bba61545ae35cc45ed41a41`.

## 2026-06-17 CPA `v7.2.13` SDK Compatibility Rebuild

| Item | Value |
|---|---|
| Ergouzi plugin target | `v0.1.4-ergouzi.3` |
| Upstream plugin latest release | `v0.1.4` |
| Upstream plugin commits pending | `0` |
| CPA host sync target | `v7.2.13` |
| CPA SDK dependency | `github.com/router-for-me/CLIProxyAPI/v7 v7.2.13` |
| Reason | Rebuild plugin against the CPA SDK version used by the pending CPA `v7.2.13` sync. |

Findings:

- Upstream `LTbinglingfeng/cpa-plugin-codex-invite` has no new commits or
  releases after `v0.1.4`.
- This is an Ergouzi compatibility rebuild, not an upstream plugin feature sync.
- The rebuild preserves `management_origin` support and the existing Ergouzi
  fork metadata.

Verification:

```bash
go test ./...
make vet
make build PLUGIN_OUTPUT=/tmp/codex-invite-v7.2.13.dylib
git diff --check
rg -n '^(<<<<<<<|=======|>>>>>>>)' .
```
