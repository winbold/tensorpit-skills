---
name: tensorpit-campaign
description: Use when someone wants to trade an idea through TensorPit — they are discussing a stock they want to invest in, they have a trade idea or research note for one ticker, or they ask to revise, finalise, test or simulate one. Drives the whole path over TensorPit's two MCP connectors (its compiler and its runner) — write the report from the discussion, ask TensorPit what shape a report must take, rewrite it to that shape, submit it, work the compiler's corrections with the user deciding every default, simulate it on its probable futures and bring the result back, rework or finalise. Also use to resume a report already submitted, or when asked how to connect Claude to TensorPit. NOT for starting, stopping or sizing live trading — that stays the account owner's step in TensorPit.
argument-hint: [ticker, or a path to the idea .md]
---

# Trade an idea through TensorPit

TensorPit turns **one report** — prose, on **one ticker** — into **one campaign**: its compiler
reads the report, checks every value against the text, asks what it cannot safely infer, and
seals the executable plan. This skill drives the whole path:

**discuss the idea → write a report → ask TensorPit what a report must look like → rewrite it to
that shape → submit it and work the corrections → simulate it in TensorPit → bring the result
back → rework or finalise → hand the owner the campaign.**

The loop at the end matters as much as the start: a result the user does not like is **not** the
end of the job. They say what to change, you change the report, it compiles again.

Five things to hold on to before anything else:

1. ⛔ **You write the report; TensorPit's compiler reads it.** The report is prose. TensorPit
   checks it, asks what it cannot safely infer, and keeps the record.
2. ⛔ **This skill never trades.** The compiler has no trading tool, and `finalize_report` finalizes the
   campaign — freezes its report — and starts nothing. A deployment may give the RUNNER trading tools
   (`start_trading`, only behind `TENSORPIT_CONSUMER_TRADE_ENABLED`); this skill does not use them. Going
   live is the account owner's step in the TensorPit webapp. Say so plainly at the end; do not offer to do
   it, and do not reach for REST to do it.
3. ⛔ **You never decide the trading.** You state *what the market might do* and *what the user
   wants*. TensorPit derives the stance, algorithms, sizes, strikes, bars, order mechanics and
   every risk limit. Write levels RELATIVE to the market ("a close below the swing low", "more
   than 5% after the print", "stop 10% from the entry"), never a hard price — unless that exact
   price matters for a reason the report gives beside it (a holder's vesting price, heavy exposure
   at an options strike). Never a share count or a strike to trade.
4. ⛔ **The user decides every default.** When the compiler asks, and the discussion does not
   settle it, put it to the user with `AskUserQuestion`. Never answer on their behalf.
5. The whole campaign is the user's money. Work in their words, quote their own numbers, and
   never invent a catalyst, a date or a probability they did not give you.

## 0 · Check the connection first

TensorPit is TWO MCP connectors: its **compiler** (`/api/v1/mcp/compiler` — you have it if you can
see `get_strategy_instructions`, `submit_report`, `get_compilation`, `revise_report` and
`finalize_report`) and its **runner** (`/api/v1/mcp/runner` — `get_runner_capabilities`,
`propose_simulation_paths`, `run_simulation`, `watch_simulation`, `get_simulation`, `list_simulations`).

- **Not connected?** Read `references/connect.md` and walk the user through it — claude.ai and
  Cowork add each as a custom connector, Claude Desktop and Claude Code take a URL and a key.
  Without the compiler there is nothing to submit to: stop there. Without the runner, steps 1–4
  and 7 still work; say step 5 needs it.
- **Connected but a call answers 401** → their token or key is stale; `references/connect.md`
  has the reconnect steps.

⛔ **Then read which caller you are.** `get_capabilities` answers with a `plugin` block, and it
decides what you may offer. TensorPit derives it from the credential — you never set it, and you
never guess it:

| `plugin.profile` | Who is there | What changes |
|---|---|---|
| `consumer` | A person, signed in, watching | Ask them the compiler's questions. Offer the simulation when `plugin.simulate` is true, and the live trade only when `plugin.trade` is. |
| `pipeline` | Nobody — a research service | Ask no questions and offer no choices. Answer the compiler's corrections yourself and file the next revision. No simulation: its paths and capital are a person's to choose, and there is no person. |

`plugin.flowEnabled` false means this flow is switched off on this deployment: say so and stop.

## 1 · Get the idea out of the user, and write it down

Two ways in, and they end in the same place:

- **A discussion** — "I think HOOD runs into the next print". This is the common one. Draw the
  idea out: what the market would have to do for this to work, what would prove it wrong, what
  is coming up on the calendar, how long they are prepared to hold, how firmly they hold it.
  Then **write the report for them** and show it.
- **A note they already have** — a markdown file, a pasted research note. Read it and pull out
  what is **there**; do not pad it.

Either way you need these, and you ask for only what is missing:

| you need | what it sounds like |
|---|---|
| **ticker** | the name being traded. One ticker per report — two names means two reports |
| **thesis** | why this is worth owning, in their words, and why *this* is the way to own it |
| **horizon** | "through Q3", "2 months", "into the March print" |
| **conviction** | how firmly it is held, in their own words ("a starter", "high conviction") |
| **scenarios** | 3–6 things the market might do — bull, base, bear, and the one that kills it. Each with how likely it is and 2–4 paths |
| **catalysts** | dated events: earnings, a decision, a lock-up, an index add — every date in full, with its year |
| **what may be traded** | shares only, or options admitted |
| **goal and targets** | accumulate, defend, distribute, hedge, stand aside, harvest, capture a profit, hold and compound, or resolve or eject; a profit target and a loss tolerance in % |

Then **say back, in four or five lines, what you have** — ticker, thesis in one sentence, the
scenarios, the catalysts with their dates, and what is still open. They correct you here, before
a single tool call, where it is cheap.

⚠️ If the idea covers several tickers, ask which one this report is for (or whether to file them
one after another). If they name sizes, keep them **out** of the report and say why — they belong
to TensorPit. If they name a price, write it relative to the market (a level, a move after an
event, a percent from the entry) unless they give a reason that exact price matters.

## 2 · Ask TensorPit what a report must look like

⛔ **Do not write the report from memory, and do not send the one from step 1 as it stands.**
Your report is in your words; TensorPit asks every author for the same discipline, versioned with
the server you are talking to. Fetch it over the connector, **every time**:

1. **`get_strategy_instructions`** — the discipline, and the first call you make. It is short and
   it is the point: a thesis is **3–6 scenarios, each with 2–4 trajectories** (what you would
   recognise, and whether it `accelerates` / `progresses` / `stalls` / `retraces` / `whipsaws` /
   `fails`), plus **campaign goals**, a **mandate** and a **market pulse**. Every strategy
   provider on this server reads the same document — it is what keeps one campaign comparable
   with the next.
2. `get_capabilities` — the `plugin` block (step 0), and what this server can run.

## 3 · Rewrite the report to that shape, and show it

Now write it: their idea, in the four sections the instructions name. Show the user what changed
in a line or two — "I have split your 'it keeps running' into an accelerates and a progresses
trajectory, because TensorPit prices each path separately."

⛔ **The trajectories are the part people skip, and the part that makes it tradeable.** A
scenario with one path is a prediction wearing a costume: the Campaign Manager has nothing to
weigh, and the `fails` path that would hand the campaign over never exists. If the user gave you
one future, give them back three and say why.

⛔ **Describe what the market is DOING, never the instrument that measures it**: "the stock
holds above its breakout", not "RSI over 50". And **state every date** — an unstated date comes
back as a question.

Do not polish forever. The compiler's corrections are a faster reading of what is wrong than
anything you can check by eye.

## 4 · File it and work the corrections

The report is prose, and TensorPit's own compiler turns it into the plan. This is the loop:

1. `submit_report { ticker?, report, title?, author? }` — `ticker` may be left out: the compiler
   names it from the report. TensorPit checks the discipline,
   **generates the `strategyId`** (⛔ never send one of your own) and queues the compile. Keep
   that id: everything about this strategy is quoted by it.
   - **Seen before.** Text you already filed under that ticker comes back `status: duplicate` with
     the earlier `strategyId`, `seenBefore` (where it stands — compiled, finalized, deleted …) and
     `ask`, the question to put to the person. So a retry is safe, and it is never a silent "same
     report". In the **consumer** profile, ⛔ ask it with `AskUserQuestion`, in its words — *"I've seen
     this report already … Do you want to compile it again?"* — offering **Compile it again** (a NEW
     campaign from the same text, every question asked afresh — so they can answer differently) or
     **Carry on with the earlier one** (not offered when it was deleted). On Compile it again:
     `submit_report` with the same text and `compile_again: true`, and keep the NEW `strategyId`. In
     the **pipeline** profile, carry on with the earlier one; only a job that is explicitly a fresh
     compile sends `compile_again: true`.
2. `get_compilation { strategy_id }` — poll it. TensorPit never calls you back. `pending` → say
   it is compiling and ask again shortly. `awaiting_confirmation` → item 6 below. `accepted` →
   step 5. To watch it LIVE instead, loop on `watch_compilation { strategy_id, cursor? }`: it
   answers with the compile's next steps — each round, its reasoning as it streams, the checks —
   waiting up to 25 s for them, the new `cursor` to pass back, and the same `view`
   `get_compilation` gives. Narrate what moves; the rules below are the same.
3. `corrections_required` → fix what `issues` names and nothing else. Each issue carries a
   **code**, a **pointer** into the strategy, the **problem** and what is **expected** — yours to act
   on — and the same finding for the person: **plain**, the report **section** it is in and the
   report's own **quote**. ⛔ Tell the person `plain` (never a code, a band or a pointer); `summary`
   is the whole in a sentence, and it is what you say out loud.
   - A `TP-Q-REPORT-*` issue is a question the compiler could not answer from the text. In the
     **consumer** profile, put it to the user with `AskUserQuestion` — it is usually a date, and
     ⛔ an unstated date is a question, never an inference (a scheduled public one — the next print,
     an FOMC — TensorPit looks up itself and shows as looked up). Then either write their answer into the
     report (item 4), or send their words as they gave them:
     `answer_questions { strategy_id, answers: [{ question_id, answer }] }` — each issue's
     `question_id` names the question. Their answer becomes part of the report's source and the
     compile resumes. In the **pipeline** profile, answer it from the research you already have,
     in a revision.
   - A `TP-Q-REPORT-CHOICE` issue carries `options`: the report states something the campaign
     would run differently — an entry style its stance does not offer, a stop on the side it never
     trades. In the **consumer** profile put the `options` to the user with `AskUserQuestion` and
     send their choice with `answer_questions` — ⛔ never choose for them. In the **pipeline**
     profile restate that entry or stop in a revision, as the research means it.
   - Everything else is a `TP-E-REPORT-*` error (blocking) or a `TP-W-REPORT-*` warning (worth
     reading). Fix the errors; mention the warnings.
4. To change ONE thing — "buy before earnings instead of standing aside" — add it as an instruction on
   top of the report: `refine_report { strategy_id, instruction }`. It overrides the report, and any
   earlier instruction, where they disagree, and the compile runs again; `get_compilation` lists the
   `refinements` in force. To rewrite the report, `revise_report { strategy_id, report, author? }` — ⛔ the **whole** report, not a patch. The
   `strategyId` is unchanged (one strategy, many revisions) and the compile starts again at once.
5. Back to 2. Two or three rounds is the loop working. A dozen is fitting the report to the
   compiler instead of to the market — stop and say what keeps failing.
6. `awaiting_confirmation` (**consumer** only — a pipeline's reading is accepted automatically and
   never stops here). The report passed every check, and TensorPit shows how it **read** it:
   `reading` carries the stance (risk appetite, conviction, horizon), the goals and loss
   tolerance, each scenario with its paths, the dated events and the mandate — each stated value
   with the report sentence it rests on. ⛔ Nothing is sealed until the user accepts this reading.
   Show it to them in their words and ask with `AskUserQuestion`: **Confirm**, or **Change
   something**. Only on Confirm: `confirm_reading { strategy_id, ast_digest }` with
   `reading.ast_digest` exactly — the campaign is sealed and the compile is `accepted`. On a
   change: item 4, a revision, which reads again. ⛔ Never confirm for them, and confirming is not
   finalising — the text can still be revised until step 7.

## 5 · Simulate it — in TensorPit, on its probable futures, with the user's money

Once the compile is `accepted`. ⛔ **First, show the user what the campaign DOES**: `get_compilation`'s
`campaign_summary`, line by line — it is written from the sealed plan, so it shows the entry and the
stop that will actually run, which may differ from the report's words. Then offer to test it.

⚠️ **Skip this step entirely unless `plugin.simulate` is true.** In the `pipeline` profile it
never is, and there is nobody to choose the paths or the capital.

⚠️ **Simulation is the RUNNER's** (its own connector): call these on `/api/v1/mcp/runner`, by the
`strategy_id` the compiler gave you. A runner connector that answers `get_runner_capabilities` with
`plugin.simulate` false offers no simulation — skip this step.

⛔ **There is no backtest.** A report's scenarios are about the coming sessions — this print, this
level — and no past window holds them, so TensorPit tests a campaign by **simulating** it: the few
futures its own scenarios make probable, rendered on the real calendar and the name's own moves.

1. `propose_simulation_paths { strategy_id }` — the paths, most probable first by the report's own
   odds (until about 80% of them), plus one **risk path** (a gap through the stop), each told in a
   sentence or two. Show them to the user, with their odds, in those words.
2. Ask which paths to run (all, by default) and the **capital**, with `AskUserQuestion` (rules below).
3. `run_simulation { strategy_id, cash, path_ids }` — it answers at once with a `simulation_id`.
   Say it is running: each path is first tuned with no model call until the campaign's own scenario
   tracking recognises it, then traded by the campaign's own Campaign Manager — a few minutes a path.
4. `watch_simulation { simulation_id, cursor, wait_ms: 20000 }` in a loop, passing back the `cursor`
   it answers with. It HOLDS the call until the next step happens, so each answer is news: the future
   started, each tuning run and whether the campaign recognised it, the real run, a heartbeat while a
   step is in flight, the outcome. **Tell the user what it is doing as it goes** — a future takes
   minutes, and silence reads as a hang. Stop when the `view.status` is no longer `running` and `more`
   is false. (`get_simulation { simulation_id }` is the same view without the events, for a one-off
   read; `list_simulations { strategy_id }` lists that campaign's.)

The campaign is also on its page in TensorPit (`<base>/strategies/campaigns/<slug>`), where
**Simulate** runs it the same way.

⛔ **Never pick the money or the paths yourself**: capital changes what the plan can hold (too
little, and every raise rounds to zero shares), and a path chosen for the user is a test they did
not ask for. ⛔ **A simulation is never evidence of edge**: the paths are made from the report's own
thesis. Say so — it shows whether the campaign finds its scenarios, enters, sizes, stops and exits
the way the report means, not whether the idea is right.

## 6 · Bring the results back, and ask what to do with them

⛔ **One simulated path is one POSSIBLE FUTURE, and the whole campaign was run against it.** Report
every future, never a summary that hides one — and lead with what HAPPENS in it (`story`), not the
report's thesis it came from (`scenario`, `trajectory`: provenance, said after).

For each future, in this order: did the campaign **recognise** it (`recognised`, and `tuning` says at
which strength, after how many runs — `recognised: false` is a finding about the REPORT: its gates do
not fire on its own scenario); did it **enter** (`result.engaged`, `fills`); and what came of it
(`totalReturnPct`, `realizedPnL` in money, `maxDrawdownPct`, **whether the stop held**), then its
`decisions` (when, what, why). `totals` gives the run at a glance — how many finished, were
recognised, traded, the fills and the realised money — and `expected_return_pct` the return weighted
by the report's own odds, as a check on the machinery, never a forecast.

⚠️ **A future the campaign never entered is not a 0% result.** Say "it never entered", never "it
made nothing": they mean different things, and only the first is a finding.

Then **put the fork to the user** with `AskUserQuestion`:

- **Rework the idea** — they have seen how it behaved and want to change something: an entry that
  came too late, a stop that let a gap through, a horizon that ran out before the move, options they
  now want admitted. Take their words, go back to **step 4** (`revise_report` or `refine_report`),
  and simulate again. Say what moved between the two.
- **Run other paths** — same report, more of its futures. Back to step 5.
- **Proceed** — they are happy. Go to step 7.

⚠️ **A path the campaign NEVER RECOGNISED is a finding about the report**, not a TensorPit fault:
`recognised: false` means its own scenario's gates did not fire on the very path that scenario
describes, even pushed harder. Say which, and offer to loosen what the report says that scenario
looks like. Likewise a path it recognised and never **traded**: an entry the path never reached.

## 7 · Finalise, then hand it over

`finalize_report { strategy_id }` — **finalize the campaign**: its report is frozen, so no revision is
accepted under that id afterwards. ⛔ Ask first, in those words ("Finalize the campaign? You won't be
able to revise it after this.") — never finalize for them. It does not trade and it starts nothing:
the campaign was sealed when its reading was confirmed — `get_compilation` says where.

Close with exactly this shape, using the host from the connector URL:

> **Finalised.** `<TICKER> · <slug>` — strategy `<strategyId>`.
> Campaign: `<base>/strategies/campaigns/<slug>`
> **To trade it, open the campaign and press Go live** — pick the account and the capital there.
> I can't start a session, and finalising never starts one by itself.

## When it goes wrong

| what you see | what it means | what to do |
|---|---|---|
| `report_rejected` (422) | not a report at all — empty, far too short, more than one ticker | read the findings; fix and submit again |
| `report_text_taken` (409) on a revision | another strategy already holds exactly this text | change the text, or work on that strategy instead |
| `report_finalized` on a revision | the text is frozen | submit a new report for the new idea |
| `report_deleted` on a revision, refinement or finalize | its compilation was deleted, so it never compiles again | ask the person; to start again, `submit_report` the text with `compile_again: true` |
| `failed` on `get_compilation` | the compile could not finish — the error says why | revise, or say what failed |
| `reading_stale` on `confirm_reading` | a revision or an answer produced a newer reading | `get_compilation` again and show the user the new `reading` |
| `not_awaiting_answers` on `answer_questions` | the compile stopped waiting on that question (an answer was first, or a revision replaced it) | `get_compilation` again and work what it says now |
| `not_compiled` on `propose_simulation_paths` / `run_simulation` | the compile is not `accepted` yet (or its reading not confirmed) | finish step 4 first |
| `no_history` / `vendor_unreachable` on `propose_simulation_paths` | TensorPit has too few recent sessions of the name to size its paths, or its data vendor did not answer | say so; try again later for the second |
| a path `failed` on `get_simulation` (`bundle_not_loadable`, `data_not_provisionable`, …) | TensorPit could not run the campaign on that path here — the error names why | say what it names; usually a revision |
| `lifecycle_blocked` on `delete_compilation` | something is running, or the campaign has live sessions — a delete never takes trading history | say so; nothing was deleted |
| a tool answers 401 | the connector's grant is stale or its key was revoked | `references/connect.md` → reconnect |

## Resuming, and other entry points

- **"What have I submitted?"** → `list_reports`, newest first; `get_report { strategy_id }` for
  one report as it was stored.
- **"Pick up where we left off"** → `get_compilation { strategy_id }`, read the issues, and carry
  on at step 4. `list_compilations` lists their compilations, with where each stands.
- **"What did the compiler think?"** → `get_compilation_rounds { strategy_id }`: every model call,
  with its reasoning and what the checks found.
- **"Delete it"** — ⛔ only when the user asks. `delete_compilation { strategy_id }` shows exactly
  what would go and returns a `confirm_token`; read it to them and ask with `AskUserQuestion`.
  Only on their yes, call it again with the `confirm_token`. It never takes a campaign that has
  traded live.

## References

- `references/connect.md` — connecting Claude to TensorPit (claude.ai and Cowork connector,
  Desktop, Claude Code), minting a key, and what to do when it refuses.

The discipline every report follows is `get_strategy_instructions` — read it from the connector
rather than from memory: it is versioned with the server you are talking to.
