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

## ProStocks

Fetch `prostocks-strategy-report/SKILL.md` at a **release tag**, and record the tag (or the commit)
with each run. TensorPit keeps its section headings, its labelled fields, its values and the word
bank's table shape stable: a change to any of them is announced before it is released.

## Versions

Releases are tagged. `VERSION.json` names the contract version and the TensorPit commit the files
were built from.

## License

All rights reserved: these files may be used with TensorPit only. See [LICENSE](LICENSE).
