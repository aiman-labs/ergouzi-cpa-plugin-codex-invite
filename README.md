# Codex Invite CLIProxyAPI Plugin

`codex-invite` is a CLIProxyAPI dynamic library plugin that exposes a
Management UI resource for sending Codex referral invite emails with an
existing Codex OAuth credential managed by CPA.

The plugin does not persist ChatGPT access tokens. At send time it reads the
selected Codex auth file through CPA's authenticated Management API, extracts
the current `access_token` and account ID, and calls:

```text
POST https://chatgpt.com/backend-api/wham/referrals/invite
```

## Configuration

The plugin does not expose invite fields in the Management Center plugin
configuration form. Plugin config is used to enable the plugin and can
optionally pin the internal CPA Management API origin:

```yaml
plugins:
  enabled: true
  configs:
    codex-invite:
      enabled: true
      priority: 1
      # Optional. Recommended when CPA is behind Cloudflare Access, a reverse
      # proxy, or a tunnel and the plugin should call the local CPA service
      # instead of looping back through the public management hostname.
      management_origin: http://127.0.0.1:8317
```

When `management_origin` is set, the plugin uses that server-side value before
any browser-provided origin. This avoids server-to-public-host loops such as:

```text
CPA container -> https://cpa.example.com -> Cloudflare Access -> CPA container
```

Without this setting, the resource page falls back to the current page origin,
which is suitable only when the CPA server can reach that origin without extra
browser-only authentication.

## Resource Page

The plugin resource page is available at:

```text
/v0/resource/plugins/codex-invite/invite
```

It provides:

- CPA management key entry for authenticated Management API calls.
- Codex credential loading and account selection from CPA auth files.
- Invite settings for referral key, ChatGPT base URL, language, originator, user agent, request email limit, and optional Cookie.
- A visible per-request proxy URL field in the invite form.
- Local browser settings for non-secret fields, excluding proxy URL.
- Invite execution through `POST /v0/management/codex-invite/invite`.

The page does not store the CPA management key, proxy URL, or Cookie in `localStorage`.
Invite details and account choice are entered in this custom page, not in the
plugin configuration form.

## Build

```bash
make test
make build
make package
```

On macOS this creates:

```text
dist/codex-invite.dylib
dist/codex-invite_0.1.4_darwin_arm64.zip
dist/codex-invite_0.1.4_darwin_arm64.zip.sha256
```

Install locally by copying the dynamic library to CPA's plugin discovery
directory, for example:

```bash
mkdir -p /path/to/CLIProxyAPI/plugins/darwin/arm64
cp dist/codex-invite.dylib /path/to/CLIProxyAPI/plugins/darwin/arm64/codex-invite.dylib
```

Target platform, output directory, and runtime plugin version can be overridden:

```bash
make build GOOS=darwin GOARCH=arm64 BUILD_DIR=/path/to/plugins/darwin/arm64
make package VERSION=0.1.4
```

## Plugin Store Release

The Ergouzi CPA plugin store registry for this plugin is published at:

```text
https://raw.githubusercontent.com/aiman-labs/ergouzi-cpa-plugin-codex-invite/main/registry.json
```

Add it to CPA `config.yaml` with:

```yaml
plugins:
  enabled: true
  store-sources:
    - https://raw.githubusercontent.com/aiman-labs/ergouzi-cpa-plugin-codex-invite/main/registry.json
  configs:
    codex-invite:
      enabled: true
      priority: 1
      management_origin: http://127.0.0.1:8317
```

The registry keeps the plugin ID as `codex-invite` so existing CPA plugin
configuration keys, management routes, and CPAMC resource links do not need a
migration.

Because the official CPA plugin store can also publish an entry with the same
plugin ID, CPAMC may show both the official source and the Ergouzi source. For
Ergouzi production, manage the entry whose source is `aiman-labs` /
`raw.githubusercontent.com`. Do not replace it with the upstream `codex-invite`
entry unless you intentionally want to leave the Ergouzi fork.

For plugin-store installation, each GitHub release must include:

```text
codex-invite_<version>_<goos>_<goarch>.zip
checksums.txt
```

Each zip must contain the dynamic library at the zip root:

- Darwin: `codex-invite.dylib`
- Linux: `codex-invite.so`
- Windows: `codex-invite.dll`

`checksums.txt` must be in sha256sum format.

When publishing a new Ergouzi plugin release, update `registry.json` on `main`
so the `version` field matches the GitHub Release asset version. The registry is
the plugin-store index; the release asset is the install/update payload.

Generate a local aggregate checksum file with:

```bash
make checksums VERSION=0.1.4
```

## Management API

The plugin registers:

- `GET /v0/management/codex-invite/accounts`
- `POST /v0/management/codex-invite/invite`
- resource page `/v0/resource/plugins/codex-invite/invite`

The resource page asks for the CPA management key because plugin iframes are
served from the CPA backend origin and cannot read the Management Center's
frontend auth store.
