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
