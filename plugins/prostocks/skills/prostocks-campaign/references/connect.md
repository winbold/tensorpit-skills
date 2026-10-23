# Connecting Claude to ProStocks

ProStocks is **one MCP server** (2026-10-22). Add one connector:

```
https://tp-dev.winbold.net/api/v1/mcp
```

It carries two components, and the skill uses both over that one connection:

| component | what it does |
|---|---|
| **the compiler** | the report loop: submit, compile, correct, confirm the reading, finalize — the campaign is sealed here |
| **the runner** | runs a sealed campaign by its strategy id: simulation (and, only where the deployment turned it on, trading) |

⛔ **The address is not the gate.** One connector does not mean one level of access. What you can
reach is your KEY'S SCOPE, and a tool your key cannot use is **not registered** — it never appears
in `tools/list`. A `reports` key sees the report tools and no simulation or trading tool at all.

Each component also has an address of its own, for an integration that wants one half and a
connector it can revoke on its own: `/api/v1/mcp/compiler` and `/api/v1/mcp/runner`. A person needs
neither.

Default host: `https://tp-dev.winbold.net`. A self-hosted box is the same paths on its own host.

⛔ **The compiler trades nothing.** The runner starts a live session only where the deployment turned
trading on (`TENSORPIT_CONSUMER_TRADE_ENABLED`, off by default) — and then only after the person confirms
each start. Stopping a session stays in the TensorPit webapp.

---

## First, mint a key (every route needs one)

In the webapp: **Settings → API Keys → New key**. The plaintext `tp_…` is shown
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
2. Name it `ProStocks`; URL: `https://tp-dev.winbold.net/api/v1/mcp`.
3. **Connect**. A consent page opens.
4. Paste the `tp_…` key and press **Approve**. Claude is sent back and the connector goes green.

⚠️ Simulation needs a key whose scope allows it — `reports-simulate` or `full`. A plain `reports`
key connects fine and gives you the whole report loop, but no simulation tool appears in the list.
Where a provider's sign-in is the consent, the scope is the federation key's, so this is the
administrator's setting rather than yours.

A token is bound to the address it was minted for: a grant for `/api/v1/mcp` is not a grant for
`/api/v1/mcp/compiler`, and the other way round.

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
and `/oauth/*` (each address's metadata is path-suffixed:
`/.well-known/oauth-protected-resource/api/v1/mcp`, `…/mcp/compiler` and `…/mcp/runner`). Set `TENSORPIT_PUBLIC_URL` to
the exact public base URL — the metadata's `resource` must equal the MCP URL as typed in Claude, path and all. Anthropic's requests come
from `160.79.104.0/21` if a firewall needs to allow them.

---

## Claude Code

Bearer, no OAuth round trip:

```bash
claude mcp add --transport http prostocks https://tp-dev.winbold.net/api/v1/mcp \
  --header "Authorization: Bearer tp_YOUR_KEY"
```

Then `/mcp` in a session lists the tools. `claude mcp remove prostocks` undoes it. The OAuth
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
    "prostocks": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://tp-dev.winbold.net/api/v1/mcp", "--header", "Authorization:Bearer tp_YOUR_KEY"]
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
| Only report tools appear; no simulation | the key's scope is `reports` | it needs `reports-simulate` (or `full`) — the address never changes what a scope allows |
| 401 on `mcp-remote` | the header argument was split | `Authorization:Bearer tp_…`, no space after the colon |
| A report compiles but the campaign shows nothing in the webapp | you are signed into the webapp as a different user | reports and their campaigns belong to the key's owner |
