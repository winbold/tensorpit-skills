# TensorPit's MCP tools

Every tool TensorPit offers over MCP, what it takes and what it answers. The workflows these
belong to are in [INTEGRATION.md](INTEGRATION.md).

TensorPit has two components, and three addresses: one for each component, and one carrying both.
Which address you call changes nothing about what you may reach — ⛔ **your credential's SCOPE
decides that**, and a tool it cannot use is not registered at all.

Read this table as a map, not as a contract on *availability*: ⛔ **a tool you cannot use is not
registered**, so what `tools/list` gives your credential is the truth. A missing tool is an answer.

- `?` marks an optional argument.
- **profile** — `consumer` means a person is here (an OAuth grant, a signed-in session); a tool marked
  `consumer` does not exist for a service key.
- **flag** — the deployment turned it on; trading is off by default.
- Every report and simulation tool acts **only on the caller's own**: someone else's id answers exactly
  as an unknown id does.

---

## The compiler · `/api/v1/mcp/compiler`

A report becomes a sealed campaign here. Reachable by a `full` key and by a `reports` key.

| tool | arguments | answers | |
|---|---|---|---|
| `get_strategy_instructions` | — | the report discipline, in full — what a report must contain and how it is judged | read |
| `get_capabilities` | — | the contract version, and the `plugin` block: your `profile`, and whether simulation and trading are on for you | read |
| `submit_report` | `report`, `ticker?`, `title?`, `author?`, `compile_again?` | `strategyId` (TensorPit generates it), `status` `queued`\|`duplicate`, advisory `checks`; on a duplicate also `seenBefore` and `ask` | writes · ⚠️ camelCase |
| `get_compilation` | `strategy_id` | where the compile stands: `status`, `issues[]` (with `plain`, `section`, `quote`), `summary`, `campaign_summary` once sealed, and — while `awaiting_confirmation` — the `reading` with its `ast_digest` | read |
| `watch_compilation` | `strategy_id`, `cursor?`, `wait_ms?` | `{ events, cursor, more, view }` — the compile's steps after `cursor`, holding the request open up to 25 s while it runs | read |
| `get_compilation_rounds` | `strategy_id`, `after_round?`, `limit?`, `reasoning?` | one row per answered model call: what was asked, what came back, usage, findings. ⚠️ The reasoning carries your report's content | read |
| `answer_questions` | `strategy_id`, `answers[{ question_id, answer }]` | the compile resumes at once; refused, naming why, when it is no longer waiting on those questions | writes · consumer |
| `revise_report` | `strategy_id`, `report`, `author?` | replaces the text under the same strategy and recompiles | writes |
| `refine_report` | `strategy_id`, `instruction`, `author?` | files one instruction on top of the report — it supersedes the report, and earlier instructions, where they conflict | writes |
| `confirm_reading` | `strategy_id`, `ast_digest` | accepts the reading and **seals the campaign**; a stale digest is refused (`reading_stale`) | writes · consumer |
| `cancel_compilation` | `strategy_id` | stops a compile that is running, queued or waiting on an answer. The model call in flight is cut. Not a delete — the report is kept and a revision compiles it again | writes |
| `finalize_report` | `strategy_id` | freezes the report text. The campaign was sealed at confirmation | writes |
| `get_report` | `strategy_id` | the report as filed, with its revisions and refinements | read |
| `list_reports` | `ticker?`, `status?` | your reports | read |
| `list_compilations` | `status?`, `ticker?` | your compilations, newest first | read |
| `delete_compilation` | `strategy_id`, `confirm_token?` | without a token: what would be deleted, and a token good for five minutes. With it: the delete. Live history is never taken | writes · consumer · `full` key |

## The runner · `/api/v1/mcp/runner`

A sealed campaign runs here, by the `strategy_id` the compiler gave you. ⛔ A `reports` key reaches
none of it.

| tool | arguments | answers | |
|---|---|---|---|
| `get_runner_capabilities` | — | which caller you are, and whether simulation and trading are on for you | read |
| `propose_simulation_paths` | `strategy_id` | the campaign's probable futures from its own scenarios — each with its odds and its story — plus one risk path, and the calibration they were sized from. **Nothing runs** | read · consumer |
| `run_simulation` | `strategy_id`, `cash`, `path_ids?` | starts it and answers at once with a `simulation_id`; the paths run behind it, minutes a path. Absent `path_ids`: all of them | writes · consumer |
| `watch_simulation` | `simulation_id`, `cursor?`, `wait_ms?` | `{ events, cursor, more, view }` — each path started, each tuning run and its verdict, the real run, a heartbeat while a step is in flight, the outcome | read · consumer |
| `get_simulation` | `simulation_id` | where it stands and what each path found: recognised, engaged, fills, return, drawdown, whether the stop held, the decisions, model calls; and the return weighted by the report's odds | read · consumer |
| `list_simulations` | `strategy_id` | that campaign's simulations, newest first | read · consumer |
| `list_trading_accounts` | — | the brokerage accounts linked in TensorPit you may trade through | read · consumer · flag |
| `start_trading` | `strategy_id`, `account_id`, `cash`, `confirm_token?` | without a token: exactly what would start, the campaign's summary, and a token good for five minutes bound to you, the strategy, the account and the capital. With it: the live session. ⛔ Places orders with a broker | writes · consumer · flag |
| `get_trading_session` | `session_id` | the session: status, symbols, cash, positions, unrealised P&L, why it failed | read · consumer · flag |

⛔ **Stopping a live session is not here.** It stays in the TensorPit webapp, with the account owner.

---

## Both together · `/api/v1/mcp`

The universal address: every tool above, on one connection. This is the address a PERSON is given,
because adding one connector is simpler than adding two.

⛔ It grants nothing extra. A `reports` key calling here lists the compiler's tools and no simulation
or trading tool — the same list it gets on `/api/v1/mcp/compiler`. Use a component's own address
when you want a connector you can revoke on its own: a token is bound to the address it was minted
for, so the three are three separate grants.

---

## Two conventions worth knowing before you write the client

**The watch contract is one shape, twice.** `watch_compilation` and `watch_simulation` both answer
`{ events, cursor, more, view }`: pass the `cursor` back next time, and treat **`more: true` as "ask
again at once"** rather than waiting — the answer was trimmed to fit a result budget. The same
contract is on REST at `GET /api/v1/simulations/{id}/events`.

**`submit_report` is the one camelCase answer.** It answers the intake service's own object —
`strategyId`, `seenBefore`, `createdAt` — while every view-shaped answer (`get_compilation`,
`get_simulation`, the proposals) is snake_case. Reading `strategy_id` off a submit gets you
`undefined`, and the next call then watches nothing.
