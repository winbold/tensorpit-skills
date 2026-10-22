# TensorPit skills

TensorPit turns a research report on one ticker into a trading campaign: its compiler reads the
report, checks every value against the text, asks what the text leaves open, and seals the
campaign; its runner simulates it, and runs it live only where the account owner starts it.

This repository is how you use TensorPit from Claude, and how ProStocks writes the reports it
compiles. It is **generated from TensorPit** — do not edit it here.

| what | where |
|---|---|
| **`prostocks-strategy-report`** — writing a ProStocks research report TensorPit compiles: the discipline, the ProStocks card sections and labels, the writing standard (ASD-STE100) and its trading word bank, filing | [`plugins/tensorpit/skills/prostocks-strategy-report/SKILL.md`](plugins/tensorpit/skills/prostocks-strategy-report/SKILL.md) |
| **`tensorpit-campaign`** — from a trade idea in a conversation to a compiled, simulated campaign, with the person deciding every default | [`plugins/tensorpit/skills/tensorpit-campaign/`](plugins/tensorpit/skills/tensorpit-campaign/) |
| **Connecting Claude** to TensorPit's two MCP servers | [`plugins/tensorpit/skills/tensorpit-campaign/references/connect.md`](plugins/tensorpit/skills/tensorpit-campaign/references/connect.md) |
| **The discipline** both skills write to (TensorPit also serves it as `get_strategy_instructions`) | [`docs/STRATEGY-INSTRUCTIONS.md`](docs/STRATEGY-INSTRUCTIONS.md) |
| **Integrating** — how a program drives TensorPit: the report loop, the confirmation gate, simulation, live trading, the runner, REST, refusals, and running it locally | [`docs/INTEGRATION.md`](docs/INTEGRATION.md) |
| **Every MCP tool**, what it takes and what it answers | [`docs/TOOLS.md`](docs/TOOLS.md) |

## Claude Code

The repository is a plugin marketplace with one plugin, `tensorpit`: both skills, and both MCP
servers (`https://tp-dev.winbold.net/api/v1/mcp/compiler` and `https://tp-dev.winbold.net/api/v1/mcp/runner`).

```
/plugin marketplace add winbold/tensorpit-skills
/plugin install tensorpit@tensorpit-skills
```

Then connect each server once from `/mcp` (`plugin:tensorpit:compiler` and
`plugin:tensorpit:runner`): it is OAuth, and the consent is a TensorPit API key, or your
provider's sign-in where one is configured. The runner needs a full key; a `reports` key reaches
the compiler only.

## claude.ai, Claude Desktop, Cowork

Add this repository as a plugin marketplace (**Customize → Plugins → Add → Add marketplace**,
`winbold/tensorpit-skills`) and install `tensorpit`: the skills load, and the two servers appear
on the plugin's Connectors tab — connect each once. Or add the two servers yourself as custom
connectors (one for `https://tp-dev.winbold.net/api/v1/mcp/compiler`, one for `https://tp-dev.winbold.net/api/v1/mcp/runner`) and
upload a skill folder as a skill. The connect guide has every step.

## Writing your own integration

You do not need Claude. Both servers are plain MCP over HTTP, and the same work answers on REST —
[`docs/INTEGRATION.md`](docs/INTEGRATION.md) is the guide, [`docs/TOOLS.md`](docs/TOOLS.md) the
reference. The short version:

1. Mint a key in the TensorPit webapp. A `reports` key reaches the compiler only; the runner needs a
   full key.
2. `get_strategy_instructions` → write the report → `submit_report` → poll `get_compilation` (or hold
   `watch_compilation` open).
3. Fix what it asks: `revise_report`, `refine_report`, or `answer_questions` in the person's words.
4. ⛔ A compile that passes seals **nothing**: a person accepts the reading with `confirm_reading`
   (a service key's is accepted automatically). `finalize_report` then freezes the text.
5. Simulate on the runner — `propose_simulation_paths` → the person picks the paths and the capital →
   `run_simulation` → `watch_simulation`. It tests the machinery, never the edge.
6. Trading, where the deployment turned it on, is two calls with the person's yes between them — and
   stopping a session stays in the TensorPit webapp.

⛔ Read `tools/list`: a tool your credential cannot use is **not registered**, which is the answer.

To run TensorPit on your own machine while you build against it — no sign-in, no database server, no
Redis — follow [§9 of the integration guide](docs/INTEGRATION.md#9--running-tensorpit-on-your-own-machine):
the `.env` it needs, what each line is for, and what each missing piece looks like.

## ProStocks

Fetch `prostocks-strategy-report/SKILL.md` at a **release tag**, and record the tag (or the commit)
with each run. TensorPit keeps its section headings, its labelled fields, its values and the word
bank's table shape stable: a change to any of them is announced before it is released.

## Versions

Releases are tagged. `VERSION.json` names the contract version and the TensorPit commit the files
were built from.

## License

All rights reserved: these files may be used with TensorPit only. See [LICENSE](LICENSE).
