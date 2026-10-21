# TensorPit Strategy Instructions — one name, one thesis (7.0.0)

**Read this before you write anything.** It is how TensorPit asks you to think about a trade
idea. It applies to every author — a strategy provider's pipeline, Claude, Perplexity, a person
at a keyboard — because the discipline has to be the same whatever wrote the report, or the
campaigns cannot be compared with each other.

It is deliberately **soft**. Nothing here is a schema to fill in. These are the sections a
strategy report must carry to be worth compiling, and the reasoning behind each. The checking is
strict and comes after.

---

## The discipline, in one line

> **A thesis is not a prediction. It is a set of scenarios, each with the paths it can take, each
> path with what you would SEE and what it means.**

A report that says *"HOOD goes to 160 by March"* cannot be traded by anything. It names one
future and no way to tell, on a Tuesday in January, whether it is still true. A report that says
*"here are the three things that can happen, here is what each looks like while it happens, and
here is the one that kills the idea"* is a campaign: it can be sized, monitored, and abandoned on
evidence rather than on nerve.

Everything below follows from that.

---

## What you send, and what TensorPit does with it

You write a **research report on one ticker, in prose.** TensorPit's report compiler reads it and
builds the executable plan — scenarios, gates, evidence, the board, the campaign. What it cannot
infer, it **asks**: a question lands in the account owner's inbox, answered in their own words,
and the compile resumes with that answer appended to the report.

So the discipline is not bureaucracy. Every section you write well is a question nobody has to
answer and a repair round the compiler does not spend.

**Yours:** what the market might do, how you would recognise it, what you want, what you are
prepared to lose, and what may be traded.

**TensorPit's:** the stance, the algorithms, position sizes, prices, strikes, bar sizes, order
mechanics, entry and exit levels, every risk limit, and which engine runs each component.

⛔ **Nothing here starts trading.** Compiling writes the plan. Going live is the account owner's
step, in TensorPit.

---

## The four sections

Every strategy report carries these. In order of how much the engine depends on them:

| # | section | the question it answers | read by |
|---|---|---|---|
| 1 | **Scenarios, with trajectories** | What might the market do, and how would I recognise it? | Scenario Monitor |
| 2 | **Campaign goals** | What am I trying to get out of this, and what am I prepared to lose? | Campaign Manager |
| 3 | **Mandate** | What is this campaign *for*, and when has it failed? | Mandate Monitor |
| 4 | **Market pulse** | What am I assuming that nobody is watching? | Market Pulse |

Plus two short pieces of framing: **context** (how firmly you hold this, and for how long) and
**what may be traded** (shares only, or options admitted — the one permission that is yours).

### 1 · Scenarios, with trajectories

**3 to 6 scenarios.** Bull, base, bear, and the one that kills the idea. Fewer than three and you
have not thought about being wrong; more than six and no two are really different.

Each scenario says:

- **its bias** — upside, downside or neutral;
- **a prior** — how likely you think it is, right now. The priors are a view, not a formality;
- **what would be doing the work** — technical, flow, fundamental, macro, event;
- **the thesis in your own words** — the headline, the driving force, what has to be true;
- **evidence** — what would *confirm* it, and what would *invalidate* it;
- **its trajectories** — 2 to 4 of them. This is the part people skip, and it is the part that
  makes the thing tradeable.

**A trajectory is one way the scenario can play out.** Each needs what you would **recognise** it
by, and which of six outcomes it is:

> `accelerates` · `progresses` · `stalls` · `retraces` · `whipsaws` · `fails`

Written out, one trajectory reads about like this:

> **Clean reclaim (about even odds).** The base holds, buyers keep defending it, and
> participation confirms the advance rather than thinning into it. That is the thesis
> **progressing**.

⛔ **Do not collapse the paths into one.** TensorPit keeps running odds on every trajectory —
prior × price evidence × time decay, renormalised each bar — and the Scenario Monitor names every
path it recognises, every bar. A scenario with one path is a prediction wearing a costume: there
is nothing to weigh, and the `fails` path that hands the campaign to its successor never exists.

### 2 · Campaign goals

What you want out of it, and what the campaign *is* — one goal: accumulate, defend, distribute,
hedge, stand aside, harvest, capture a profit, hold and compound, or resolve or eject; a profit
target and a loss tolerance **as percentages** (moves in the price from the entry); what you end up holding; whether it may re-enter
after a stop, and on what.

Say how it should behave **differently once it is holding** than when it is flat. Most reports
that trade badly are the ones that never said.

⚠️ The loss tolerance is not decoration — it is the number the risk rules act on. Half of it
triggers a review; all of it takes the campaign to its floor.

### 3 · Mandate

Why this campaign exists, and the conditions under which it has failed *even if it has not lost
money yet* — "the tape stops producing clean triggers and cycles die between objective and stop"
is a real failure criterion.

The failure criteria are what authors leave out, and what saves money: the Mandate Monitor
reviews the campaign against them on a schedule and escalates when they are met.

### 4 · Market pulse

What you are **assuming** without watching it, and what you know you cannot handicap:

- an **assumption** you are leaning on ("financial conditions stay accommodative");
- an **unknown** — a known event you cannot price ("a regulatory decision lands in the window");
- the **residual** — "price does something none of the scenarios describes";
- an **opportunity** — a condition under which the campaign should do *more* than planned.

A report with no pulse believes nothing outside its own three scenarios can matter. Include the
residual at minimum.

---

## The four habits that save a round

These are what the compiler most often has to ask about. Each one is cheap to write and expensive
to leave out.

**1 · Describe what the MARKET is doing, never the instrument that measures it.**
*"Participation confirms the advance"*, not *"volume > 20-day SMA"*. *"The level buyers defended
through the summer is holding"*, not *"price > $94.50"*. TensorPit picks the measurement, the
period and the threshold — and on the report path, behaviour written this way is what lets the
compiler bind a reading from its own vocabulary instead of stopping to ask you.

**Prefer a level RELATIVE to something the market will show, never a hard price.** Say "a close
below the recent swing low", "a move of more than 5% after the print", "a gap of 3% or more",
"stop 10% from the entry" — not "$354". A hard price goes stale as the stock moves, and on another
day or in a simulation it means nothing. State one only when that exact price matters for a REASON,
and give the reason in the same sentence: an institutional holder's vesting price, heavy exposure at
an options strike. Share counts, position sizes and which strike to trade stay TensorPit's — never
state them.

**2 · State every date.** Earnings, a decision, a lock-up, an index add — with the date, in the
report. ⛔ An unstated date is a **question**, never a guess: the compile pauses and the account
owner has to answer it in their own words before it resumes. A date with no year is read as its
next occurrence, which is rarely what you meant. A SCHEDULED public date — the ticker's next print and
whether it comes before the open or after the close, an FOMC or CPI release — TensorPit looks up from
the public calendar and shows where it came from, so leaving one out costs no question; a date you state
that the calendar contradicts is asked about.

**3 · Say each thing once, plainly, in a sentence that can be quoted.** Every condition, every
piece of evidence and every path has to be traceable to a line of your report. A thesis spread
across four paragraphs and a footnote is one the compiler cannot cite — and anything it cannot
cite, it asks about.

**4 · Make conditions that can actually turn.** A condition that always holds, and one that can
never hold, are both refused. "The stock is above zero" is not a gate; neither is "the stock
doubles overnight while volume halves".

---

## When TensorPit pushes back

The compiler checks what it read from your report, repairs its own reading over up to eight rounds,
and asks — the account owner for a file report, you for a submitted one — only when it genuinely
cannot proceed. A submitted report's questions come back on `get_compilation`; answer by revising
the report (`revise_report`), and finalize it (`finalize_report`) when the reading is right.

The ones first attempts hit:

| what it means | what to do |
|---|---|
| you named an instrument or a size, or a hard price with no reason | describe the behaviour, and the level relative to the market |
| a scenario can never ready an entry | its conditions cannot all hold at once — loosen or split them |
| the entry has no level to key on | no scenario observes structure; say what level matters and why |
| an event has no date, or two events could be meant | name the date, and name which event |
| the risk appetite, conviction or horizon is not stated | state each in a sentence — TensorPit derives the stance from them and never guesses one |

The full list is `docs/report-compiler-diagnostics.md`.

---

## How a report reaches TensorPit

- **Submitted**, the ordinary way: `submit_report` on the MCP connector, or
  `POST /api/v1/reports` (`{ticker, report, title?, author?}`). TensorPit checks it against this
  discipline, **generates its `strategy_id`** — a UUID — and queues it for the compiler. The id
  comes back to you, and it is what everything about this strategy is keyed on afterwards: the
  compilation, the compiled plan, the campaign. ⛔ You never choose it, and you never need to.
- **As a file**: one markdown report per ticker, dropped where the server polls for it
  (`<TENSORPIT_REPORTS_DIR>/<TICKER>/<strategy_id>.md`), with the id in the filename. Unchanged,
  and read beside the submitted ones.
A submitted report is **revised** under its one strategy id until you finalize it:
`submit_report` → `get_compilation` → `revise_report` → `finalize_report` (REST:
`/api/v1/reports/{strategyId}/{compilation,revise,finalize}`). Once finalized it is compiled for
good; a new idea is a new report and a new id.

⚠️ Submitting returns **warnings** where the discipline is thin — missing trajectories, an event
with no date, an indicator where the market should be. They do not block: the report is stored
and the compiler is the strict step. They are the cheapest round you will ever save.

---

## The rest of the kit

| | |
|---|---|
| `prostocks/SKILL.md` | the provider skill — self-contained: the discipline, the rules that decide whether a report compiles first time, a worked example, and the pre-flight checklist. A ProStocks agent needs this file and nothing else |
| `claude/tensorpit-campaign/` | the Claude skill: the report loop on the MCP connector, step by step |

⚠️ **The JSON is what a report BECOMES, not what you have to write by hand.** It is the
compiler's output and the record kept beside the compiled plan. This document is the thing to
think in.
