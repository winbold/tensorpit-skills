# Connecting Claude to TensorPit

TensorPit is **two MCP servers** (2026-10-21), and Claude connects to each natively:

| connector | URL path | what it does |
|---|---|---|
| **TensorPit compiler** | `/api/v1/mcp/compiler` | the report loop: submit, compile, correct, confirm the reading, finalize — the campaign is sealed here |
| **TensorPit runner** | `/api/v1/mcp/runner` | runs a sealed campaign by its strategy id: simulation (and, only where the deployment turned it on, trading) |

Add **both**: the skill writes and compiles on the compiler, and simulates on the runner. (The old
single `/api/v1/mcp` is gone, with no alias.)

Default host: `https://tp-dev.winbold.net`. A self-hosted box is the same paths on its own host.

⛔ **The compiler trades nothing.** The runner starts a live session only where the deployment turned
trading on (`TENSORPIT_CONSUMER_TRADE_ENABLED`, off by default) — and then only after the person confirms
each start. Stopping a session stays in the TensorPit webapp.

---

## First, mint a key (every route needs one)

In the TensorPit webapp: **Settings → API Keys → New key**. The plaintext `tp_…` is shown
**once** — copy it then.

⚠️ **A key acts as the user who minted it.** Reports, compilations and simulations made with it are
theirs, on every surface. Keys minted before 2026-10-03 act as `owner`. Key management is
first-party only: it cannot be done over the API, only in the webapp.

---

## claude.ai, Claude Desktop, mobile, Cowork — a custom connector

These surfaces reach a remote MCP server over **OAuth only** — their connector dialog has no
field for a bearer token. TensorPit is its own authorization server for exactly this, and the
consent step is your API key.

1. **Settings → Connectors → Add custom connector**.
2. Name it `TensorPit compiler`; URL: `https://tp-dev.winbold.net/api/v1/mcp/compiler`.
3. **Connect**. A TensorPit page opens: *Connect Claude to TensorPit's compiler*.
4. Paste the `tp_…` key and press **Approve**. Claude is sent back and the connector goes green.
5. Again for the runner: name `TensorPit runner`, URL `https://tp-dev.winbold.net/api/v1/mcp/runner`.
   ⚠️ The runner needs a **full** key: a `reports` key reaches the compiler only.

Each connector is its own grant: a token for one is not the other's.

What that grant is:

- It acts as **the owner of the key you pasted** — same identity as using the key directly.
- **Revoking the key in Settings → API Keys disconnects the connector**, at once. That is the
  off switch.
- The key itself is never stored by TensorPit or sent to Anthropic: it is exchanged for an
  access token (1 h) and a rotating refresh token.
- Scope: `tensorpit:drafts` (the name predates reports) — bound to the connector's own server.

**Self-hosting note.** For the connector to work, three things must be publicly reachable on
the same host the user typed, with no login page in front of them (no Cloudflare Access, no
basic auth): `/.well-known/oauth-protected-resource`, `/.well-known/oauth-authorization-server`
and `/oauth/*` (each surface's metadata is path-suffixed:
`/.well-known/oauth-protected-resource/api/v1/mcp/compiler` and `…/runner`). Set `TENSORPIT_PUBLIC_URL` to
the exact public base URL — the metadata's `resource` must equal the MCP URL as typed in Claude, path and all. Anthropic's requests come
from `160.79.104.0/21` if a firewall needs to allow them.

---

## Claude Code

Bearer, no OAuth round trip:

```bash
claude mcp add --transport http tensorpit-compiler https://tp-dev.winbold.net/api/v1/mcp/compiler \
  --header "Authorization: Bearer tp_YOUR_KEY"
```

```bash
claude mcp add --transport http tensorpit-runner https://tp-dev.winbold.net/api/v1/mcp/runner \
  --header "Authorization: Bearer tp_YOUR_KEY"
```

Then `/mcp` in a session lists the tools. `claude mcp remove tensorpit-compiler` (and `-runner`) undoes it. The OAuth
flow works here too (Claude Code opens a loopback callback) if you would rather not keep a key
in the client config.

---

## Claude Desktop without the connector UI (`mcp-remote`)

A bridge, for a Desktop that cannot add custom connectors — it speaks stdio to Claude and
Streamable HTTP to TensorPit:

```jsonc
// claude_desktop_config.json
{
  "mcpServers": {
    "tensorpit-compiler": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://tp-dev.winbold.net/api/v1/mcp/compiler", "--header", "Authorization:Bearer tp_YOUR_KEY"]
    },
    "tensorpit-runner": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://tp-dev.winbold.net/api/v1/mcp/runner", "--header", "Authorization:Bearer tp_YOUR_KEY"]
    }
  }
}
```

⚠️ `Authorization:Bearer tp_…` with **no space after the colon** — some shells and launchers
split the argument on the space and the header arrives truncated. Restart Desktop after editing.

---

## When it refuses

| symptom | cause | fix |
|---|---|---|
| "Couldn't reach the MCP server" when adding the connector | discovery failed — no `WWW-Authenticate` pointer on the 401, or `/.well-known/*` not served on that host | check the three public paths above; check `TENSORPIT_PUBLIC_URL` matches the URL typed |
| Consent page says "That key is not valid" | key mistyped, truncated, or revoked | mint a fresh one; paste the whole `tp_…` value |
| Connector was green, now every tool answers 401 | the API key that granted it was revoked, or the refresh token was replayed | reconnect: remove the connector and add it again |
| "Unknown application" on the consent page | the registration was dropped (a reset database) | remove the connector in Claude and add it again |
| Tools missing in Claude Code after `mcp add` | wrong transport | it is `--transport http`, not sse |
| 401 on `mcp-remote` | the header argument was split | `Authorization:Bearer tp_…`, no space after the colon |
| A report compiles but the campaign shows nothing in the webapp | you are signed into the webapp as a different user | reports and their campaigns belong to the key's owner |
