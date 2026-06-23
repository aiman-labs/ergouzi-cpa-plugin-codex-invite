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

## 2026-06-18 CPA / CPAMC Deploy Check

| Item | Value |
|---|---|
| CPA deployed tag | `v7.2.16-ergouzi.1` |
| CPAMC deployed tag | `v1.16.11-ergouzi.1` |
| Upstream plugin latest release | `v0.1.4` |
| Plugin runtime update | none |
| Production action | skipped plugin deployment |

Decision:

- The 2026-06-18 production deploy updated CPA and CPAMC only.
- No Codex Invite plugin runtime code or release asset changed in this round.
- Skipping plugin deployment was intentional. Rebuild/deploy this plugin only
  when upstream plugin changes, Ergouzi plugin code changes, or CPA host / SDK
  compatibility requires a new `.so`.

## 2026-06-22 CPA `v7.2.27` SDK Compatibility Recheck

| Item | Value |
|---|---|
| Ergouzi plugin source | `dfc1565` |
| Upstream plugin latest release | `v0.1.4` |
| Upstream plugin commits pending | `0` |
| CPA host sync target | `v7.2.27` |
| CPA host branch | `sync/upstream-v7.2.27` |
| CPA host target commit | `1f2504eb` |
| Reason | Recheck plugin source compatibility against the CPA SDK/API changes adopted by the pending CPA `v7.2.27` sync. |

Findings:

- Upstream `LTbinglingfeng/cpa-plugin-codex-invite` still has no new commits or
  releases after `v0.1.4`.
- This is a compatibility recheck, not a plugin feature sync and not a release
  asset rebuild.
- CPA `v7.2.27` changes plugin API / ABI structures in a backward-compatible
  way:
  - `pluginapi.AuthParseResponse` and `AuthLoginPollResponse` gained `Auths`.
  - `pluginabi.Error` gained `HTTPStatus`.
- The current Codex Invite plugin does not depend on the changed auth-provider
  multi-auth fields and still builds against the synced local CPA module.

Verification:

```bash
go work init ../ergouzi-cpa-plugin-codex-invite ../ergouzi-CLIProxyAPI
GOWORK=<temp>/go.work make test
GOWORK=<temp>/go.work make vet
GOWORK=<temp>/go.work make build PLUGIN_OUTPUT=/tmp/codex-invite-v7.2.27.dylib
```

Result:

- `make test` passed.
- `make vet` passed.
- Local `darwin/arm64` dynamic-library build passed.
- No plugin source, dependency, package, release, or production deployment
  change was made for this recheck.

## 2026-06-23 CPA `v7.2.31` SDK Compatibility Recheck

| Item | Value |
|---|---|
| Ergouzi plugin source | `6e52f43` |
| Upstream plugin latest release | `v0.1.4` |
| Upstream plugin commits pending | `0` |
| CPA host sync target | `v7.2.31` |
| CPA host branch | `sync/upstream-v7.2.31` |
| CPA host target commit | `05d1792d` |
| Reason | Recheck plugin source compatibility against the CPA SDK/runtime changes adopted by the pending CPA `v7.2.31` sync. |

Findings:

- Upstream `LTbinglingfeng/cpa-plugin-codex-invite` still has no new commits or
  releases after `v0.1.4`.
- This is a compatibility recheck, not a plugin feature sync and not a release
  asset rebuild.
- CPA `v7.2.31` adds public SDK packages for plugin host and plugin store
  management, but the current Codex Invite plugin does not import those new
  surfaces and still builds against the synced local CPA module.

Verification:

```bash
go work init ../ergouzi-cpa-plugin-codex-invite ../ergouzi-CLIProxyAPI
GOWORK=<temp>/go.work make test
GOWORK=<temp>/go.work make vet
GOWORK=<temp>/go.work make build PLUGIN_OUTPUT=/tmp/codex-invite-v7.2.31.dylib
```

Result:

- `make test` passed.
- `make vet` passed.
- Local `darwin/arm64` dynamic-library build passed.
- No plugin source, dependency, package, release, or production deployment
  change was made for this recheck.
