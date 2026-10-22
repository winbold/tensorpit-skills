---
name: prostocks-strategy-report
description: >-
  Use when generating a ProStocks research report that TensorPit will compile into a trading
  campaign — one ticker, one thesis. Covers the discipline TensorPit requires (scenarios with
  trajectories, campaign goals, mandate, market pulse), the ProStocks card sections and their exact
  labels, the writing standard (ASD-STE100) and its trading word bank, the dates and quotes the
  compiler needs, what must never appear, and how to file the report. Not for reports that are not
  meant to be traded.
argument-hint: <TICKER> <the thesis in a sentence> [catalysts with dates]
---

# Writing a strategy report TensorPit can trade

You write a research report. TensorPit's compiler reads it and builds an executable campaign —
scenarios, entry conditions, evidence, the board, the risk rules. Nothing else sits between your
words and a live position, so the report has to carry everything a trader would need, and nothing
you would not stand behind if a trader acted on it.

The same report also fills the **ProStocks position card**. ProStocks' extraction step reads its
labelled sections and fields, so their headings, labels and values are spelled exactly as this
document spells them.

This document is the whole job. You do not need TensorPit's schema, its JSON, or any of its
tooling: those are the compiler's business. TensorPit owns this skill (2026-10-21); it is published
with its version at `github.com/winbold/prostocks-skills`.

---

## The discipline, in one line

> **A thesis is not a prediction. It is a set of scenarios, each with the paths it can take, each
> path with what you would SEE and what it means.**

"PLTR goes to 60 by March" cannot be traded by anything. It names one future and gives no way to
tell, on a Tuesday in January, whether it is still true. "Here are the three things that can
happen, here is what each looks like while it is happening, and here is the one that kills it" is
a campaign: it can be sized, monitored, and abandoned on evidence instead of nerve.

---

## What you deliver

**One markdown file. One ticker. One thesis.** Two theses on the same name are two reports; two
tickers are two reports.

The compiler checks every value it reads against your text. What the text does not settle, it
asks about: a report submitted over the connector or REST comes back to you with the questions
(answer them in a revision); a report dropped as a file asks the **account owner**, in plain
language, and their answer is appended to your report. Every question you prevent is a round you
do not cost anyone.

---

## Two readers, one report

| reader | what it reads |
|---|---|
| **TensorPit's compiler** | EVERY section. It quotes each value from your sentences, so what you state is what the campaign does. |
| **ProStocks' card** | The labelled sections and fields — the section invariants below. |

TensorPit's discipline lives in four sections, and the card's commit sections sit beside them:

| # | section | the question it answers |
|---|---|---|
| 1 | **Scenarios, with trajectories** | What might the market do, and how would I recognise it? |
| 2 | **Campaign goals** (in `## Primary campaign`) | What am I trying to get out of this, and what am I prepared to lose? |
| 3 | **Mandate** | What is this campaign *for*, and when has it failed? |
| 4 | **Market pulse** | What am I assuming that nobody is watching? |
| — | **Stance and execution** (`## Stance`, `## Deployment`, `## Plan bullets`, `## Execution plan`, `## Action steps`, `## Rules`, `## Why this plan`) | How decisively does the plan commit, and how does it behave once it holds? |

Plus a short **thesis** (what is happening, how firmly you hold it, for how long), a **catalysts**
list with dates, and one line on **what may be traded** (shares only, or options admitted).

### 1 · Scenarios, with trajectories

**3 to 6 scenarios.** Bull, base, bear, and the one that kills the idea. Fewer than three and you
have not thought about being wrong; more than six and no two are really different. Give each one
a *driving force* — a technical story, an earnings story, a macro story — not three sizes of the
same move.

Each scenario states, on its own labelled lines:

- **a direction** — `**Bias:**` upside, downside, or neutral;
- **a probability** — `**Probability:**`, and ⛔ **the scenario probabilities must add to 100%**;
- **the driving force** — `**Driving force:**`;
- **the thesis in plain words** — what has to be true for it;
- **what would confirm it, and what would kill it** — the `**Thesis / Confirming / Invalidating
  observables:**` lines, as market behaviour;
- **2 to 4 trajectories**, under `**Paths:**`.

**A trajectory — a path — is one way the scenario plays out.** Each needs what you would
**recognise** it by, and which of six outcomes it is:

> `accelerates` · `progresses` · `stalls` · `retraces` · `whipsaws` · `fails`

The outcome says what the path MEANS for its own scenario — never what to do about it. The engine
decides that, weighing every path's odds.

⛔ **Every path except a `stalls` path needs recognition conditions.** Time alone moves a stall's
odds; everything else has to be visible in price and participation, or the compiler cannot tell
when it is happening.

⛔ **Name the successor.** A `fails` path names which of your other scenarios takes over — by its
scenario key. It is what hands the campaign on instead of ending it.

⚠️ **Give the paths probabilities that add to 100% within the scenario.** They are renormalised
otherwise, which may not be the proportions you meant.

### 2 · Campaign goals

They sit in `## Primary campaign`, in percentages:

- **the goal** — `**Goal:**`, one of the goal values below;
- **an upside target** and **a max loss** — `**Outcome:**`, each a move in the PRICE from the
  entry, as a percentage (`Upside target: +12%`, `Max loss: -6%`), never a share of the capital;
- **what you expect to be holding at the end** — `**End holding:**` stock or cash;
- **how it behaves once it is holding** compared with when it is flat — `**Once holding:**`.
  Most reports that trade badly never said;
- **whether it may re-enter** after a stop, and on what — `**Reentry:**` in `## Execution plan`.

⚠️ The max loss is the number the risk rules act on. Half of it triggers a review; all of it takes
the campaign to its floor. It is not decoration.

### 3 · Mandate

What this campaign is for, and **the conditions under which it has failed even if it has not lost
money yet** — "price stops giving clean entries, and each attempt dies between the objective
and the stop" is a real failure criterion.

The failure criteria are the part authors leave out and the one that saves money: the campaign is
reviewed against them on a schedule, and escalated to the owner when they are met.

### 4 · Market pulse

What you are assuming without watching it:

- an **assumption** you are leaning on — "financial conditions stay accommodative";
- an **unknown** — a dated event you cannot handicap;
- the **residual** — "price does something none of these scenarios describes";
- an **opportunity** — a condition under which the campaign should do *more* than planned.

Include the residual at minimum. A report with no pulse believes nothing outside its own
scenarios can matter.

### Stance and execution

The card's commit sections — the Stance headline, the staging, the aggression, the risk posture,
the capital velocity, the deployment line, the execution plan, the action steps and the rules —
are YOUR stated intent, and TensorPit's compiler reads them as such (rule 3). Write the ones the
evidence supports, make them agree with each other and with the scenarios, and leave out what you
do not mean.

---

## The sixteen hard rules

Each one is a real failure the compiler or the card reports. They are cheap to satisfy while
writing and expensive afterwards.

**1 · The ticker's OWN earnings report must be in the catalysts if the thesis turns on earnings.**
A peer's earnings report, the sector's, or the market's never counts. If two of its own earnings
reports are in the window, make clear which one each statement means.

**2 · Prefer relative levels to hard prices.** "A close below the recent swing low", "more than 5%
after the earnings report", "stop 10% from the entry" — not "$354": a hard price goes stale as the
stock moves and means nothing on another day or in a simulation. Name one only when that exact
price matters for a reason, and give the reason in the same sentence (an institutional holder's
vesting price, heavy exposure at an options strike). Share counts, sizes and which strike to trade
are TensorPit's.

**3 · State the stance you mean, and keep it consistent.** The Stance headline, `**Staging:**`,
`**Aggression:**`, `**Risk posture:**`, `**Capital velocity:**` and the deployment line are read as
your stated intent, quoted like every other value. The Stance headline IS the staging and the
aggression in words: "COMMIT IN ONE DECISIVE STEP" on a `tranched` / `patient` plan is refused.
⛔ A campaign-level word — `patient`, `decisive`, `tranched`, `high` patience — describes the whole
campaign. Never write it into a scenario or a path: a path says what the market does and which of
the six outcomes it is, nothing else.

**4 · Give readings a time span when it matters** — "over the last 20 sessions", "through the
last three months". Spans, not anchors ("since the June low" is not a span). ⚠️ Keep spans under
roughly six months when the campaign is meant to trade intraday; a long market regime is fine, but
say it is the market regime.

**5 · Ground every number in the inputs.** The CA session has the dimensions,
the signal and the thesis. The envelope has the horizon and the
conviction. The worker evidence has the catalysts, the insider flow, the
options skew. If a number isn't in one of those inputs, DO NOT invent it.
Prefer leaving a sentence out over writing a wrong fact.

The CA session also carries price levels. Rule 2 still applies to them:
read them to decide WHICH level matters, then describe that level by what
happens there — its price only when that exact price matters, with the reason.

**6 · Describe what the MARKET is doing, never the instrument that measures it.**
"Participation confirms the advance", not "volume > 20-day SMA". "The level buyers defended
through the summer is holding", not "price > $94.50". TensorPit picks the measurement, the period
and the threshold — and behaviour written this way is what lets it bind a reading instead of
stopping to ask. The word bank's indicator table lists what to write instead.

**7 · Conviction drives commitment, not analysis.** The Z (conviction)
envelope digit tells YOU — the planner — how decisively to COMMIT, not
how certain the CA analysis is. Z1 = low conviction, Z9 = high. CA has
already told us what it thinks. Z tells YOU how to size and stage.

**8 · The horizon is pacing, not a deadline.** When you write a duration,
write it as "about X" or "up to X" — never as a countdown or an expiry
date. A campaign ends when its thesis resolves or breaks, not on a
calendar date.

**9 · Every date in full, with its year, in a sentence that states it.** "Q3 earnings on
2026-11-04, after the close." ⛔ "Early November" and "next quarter" are **not dates** — the
compiler will not guess, and somebody gets asked. A date without a year is read as the next
occurrence, which is often not what you meant.

**10 · One idea per sentence, written so it can be quoted.** Every condition, every piece of
evidence and every path has to be traceable to a sentence in your report — that is what makes the
plan auditable. A thesis spread across four paragraphs and a footnote cannot be cited, and what
cannot be cited gets asked about.

**11 · Say what confirms a scenario SEPARATELY from what triggers it.** If your evidence repeats
the entry condition word for word, the campaign has no independent way to gain or lose confidence
in the thesis.

**12 · Name the sector or peer group explicitly** if the thesis rests on relative strength — "it
is outperforming the regional-bank sector (KRE)". A comparison with nothing named cannot be
measured.

**13 · Avoid conditions that can never turn.** A condition that always holds and one that can
never hold are both refused. "The stock is above zero" is not an entry gate; nor is "the stock
doubles overnight while participation halves".

**14 · Keep each scenario's clock inside the campaign's.** A scenario cannot play out after the
campaign has ended.

**15 · Write the prose in ASD-STE100.** Every sentence follows the writing
standard below, and every trading term comes from the word bank. Rules 2
and 6 come first: the standard never brings back an indicator name, a
share count or a strike, or a price that rule 2 does not allow.

**16 · Say "after the earnings report has happened" rather than "in the post-earnings phase"**
when you mean the event is behind you. The phase turns over one bar before an after-hours earnings
report, so the literal reading opens before the event you are waiting for.

---

## Writing so the readings bind

TensorPit can measure a wide range of market behaviour. Write in these terms and it will find the
reading itself:

| what you can say | reads as |
|---|---|
| trend, momentum, the move rolling over or extending | trend and momentum readings |
| a level holding or breaking, a range, a prior high/low reclaimed | structure and key levels |
| participation, accumulation, distribution, money flow | volume and flow readings |
| the move stretched or washed out | mean reversion, oscillator extremes |
| quiet, coiled, expanding, volatile | volatility regime, compression, range expansion |
| outperforming or lagging a named sector or peer | relative strength (name the comparison) |
| a gap, and whether it fills | gap readings |
| options priced for a big move, skew, implied volatility high or low | options readings (only if options are admitted) |
| how long until the earnings report, whether it has happened | the calendar and the earnings cycle (dates required) |

⚠️ Some readings exist only on intraday campaigns (opening drive, time of day, the closing push)
and some cannot be used at all. If the compiler cannot make a reading post, it substitutes an
equivalent and records that it did — or asks. Writing in behaviour, not in indicators, is what
keeps that rare.

---

## Writing standard — ASD-STE100 (Simplified Technical English)

The report's prose follows ASD-STE100, the controlled-language standard for
technical writing. The rules below are the parts that apply to a trading
report, in our own words. The trading terms are in the **Word bank** section.

Rules 2 and 6 come first. The standard makes a sentence clear; it never makes
it name an indicator, a share count or a strike, or a price that rule 2 does
not allow. Describe the level by what happens there and the market by its
behaviour, as the readings table above says.

### What the standard covers — and what it never changes

The standard applies to the prose: the executive summary, the rationale
paragraphs, the thesis, the catalysts, the scenario paragraphs and summaries,
the paths, observables, the mandate, the market pulse, the execution paragraph,
action steps, rules, the deployment lines, the plan bullets and "Why this plan".

It NEVER changes these. Write them exactly as the template and the vocabulary
say:

- section headings and sub-field labels (`## Market pulse`, `**Paths:**`, `**When:**`)
- enum values (`tranched`, `full_book`, `upside`, `on-signal`, the path outcomes
  such as `ACCELERATES`) and the `**Goal:**` value
- the one-line Stance headline (all capitals, headline form)
- tickers, company names and event names (FOMC, CPI)
- the numbers a report may state: percentages, probabilities, ratios,
  durations and dates (and a price only as rule 2 allows)
- the `→` separator in Rules and the `X–Y%` pattern in Capital velocity and Deployment

If a writing rule conflicts with the section layout, a label or an enum, the
layout, the label and the enum win.

### Words

1. Use the word bank's PREFERRED term for every trading concept, every time.
   Do not use a synonym to vary the style: if the report says "stop" once, it
   says "stop" everywhere — never "stop-loss", "SL" or "exit level".
2. One word, one meaning. Do not use a word bank term with a different
   meaning (for example, "target" is only the level where the plan takes profit).
3. For general words, use the simplest common word that has one clear meaning:
   "use", not "utilise"; "start", not "commence"; "about", not "approximately".
4. Use a technical noun only as a noun. Do not make a verb from it: write
   "the plan exits at the stop", not "the position gets stopped".
5. Do not use slang, idioms or metaphors: no "dry powder", "pull the trigger",
   "catch a falling knife", "rip", "tank" or "melt up". The word bank lists the
   plain term to use instead.
6. Do not use contractions ("do not", not "don't"), exclamation marks or
   rhetorical questions.

### Sentences

7. Instructions — plan bullets, action steps, rules, the deployment lines —
   have no more than 20 words. Descriptive sentences have no more than 25.
8. Write one instruction in each sentence, and one topic in each sentence.
9. Use the active voice. Write instructions in the imperative: "Buy 30% of
   capital when the level buyers defended holds.", "Exit when that level breaks."
10. Use simple tenses: present, past and future. Do not start a sentence with
    an "-ing" word, and do not use an "-ing" word as a noun ("Sell when the
    price rises", not "Selling into strength"). "-ing" words that are part of a
    word bank term are correct ("trailing stop").
11. Put a condition first: "If the old range high breaks, sell 50% of the position."
    Write one condition in each sentence when you can.
12. Keep "the", "a" and "of". Do not write in telegraph style ("Entry on hold
    of level"). The Stance line and the labelled sub-fields are the only
    places where short headline forms are correct.
13. Do not make a group of more than three nouns ("post-earnings range
    breakout level"). Use "of", "for" or a second sentence.
14. For the numbers a report may state, give the number and its unit: "30% of
    capital", "2 sessions", "about 4 weeks", "2026-11-04". Do not write "a few",
    "significant" or "soon" when the inputs give a number.
15. Do not promise an outcome. "The plan aims for +12%" is correct; "the
    campaign will yield 12%" is not.

### Paragraphs

16. Each paragraph has one topic and no more than 6 sentences.
17. Start the paragraph with its most important statement.
18. Use a vertical list for a sequence of steps.

---

## Word bank — trading terms for ASD-STE100

ASD-STE100 lets a subject field approve its own **technical nouns** (TN) and
**technical verbs** (TV). This is the approved list for these reports. Use the
preferred term with the meaning given; do not use the words in the last column.
For general words that are not trading terms, use the simplest common word
(writing standard, rule 3).

Rules 2 and 6 come first: a level is named by what happens there — its price
only when that exact price matters, with the reason (rule 2) — and the market
by its behaviour, never by the indicator that measures it. "Do not name in the report" lists the indicator words and what to write
instead. Write an abbreviation in full the first time, with
the abbreviation after it — "implied volatility (IV)" — unless this list writes
it as the preferred term.

### Position and capital

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| position | TN | the shares (or options) the plan holds | exposure, book (for capital or a position), holding (for a position), bag |
| position size | TN | the share of capital in one position, as % | sizing, allocation, weight (for position size) |
| capital | TN | the money the plan can use | funds (for capital), book (for capital or a position), allocation, bankroll |
| cash | TN | capital that is not in the market | dry powder, the sidelines, ammo, firepower |
| tranche | TN | one part of an entry that is done in steps | clip, slice, lot (for a tranche), bite |
| runner | TN | the part of the position kept after a target | moon bag, tail (for a runner), free shares |
| average entry price | TN | the average price paid for the position | cost basis, avg cost, entry (for a price) |
| break-even price | TN | the price where the position has no gain and no loss | scratch (for break-even), flat (for a price) |
| capital velocity | TN | how fast capital goes into the market, as % of capital | deployment speed, pace of capital |
| capital at risk | TN | the share of capital that the plan can lose | risk budget, heat |
| max loss | TN | the largest loss the plan accepts, as % | worst case, pain threshold |
| upside target | TN | the gain the plan aims for, as % | potential (for the upside target), payoff |
| drawdown | TN | a fall from the highest value of the position, as % | giveback, heat |
| gain / loss | TN | the change in the value of the position | P&L (in prose), PnL, profit and loss |

### Orders and execution

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| order | TN | an instruction to buy or sell | ticket, trade (for an instruction) |
| limit order | TN | an order at a stated price or better | limit (alone), resting bid |
| market order | TN | an order at the current price | market (alone), at-market |
| stop order | TN | an order that starts when the price reaches the stop | stop-market, hard stop |
| fill | TN | the price and quantity of an order that is done | execution (for a fill), print (for a fill) |
| slippage | TN | the difference between the order price and the fill price | skid, leakage |
| entry | TN | the first buy of the position | initiation, opening trade |
| exit | TN | the sale of all of the position | close-out, unwind |

### Levels — named by what happens there (rule 2)

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| level | TN | a price area the evidence shows is important, named by what happens there ("the old range high") | line (for a level), mark (for a level), area (for a level), zone (for one price) |
| support | TN | a level where buyers stopped a fall before | floor (for support), base (for a level), shelf |
| resistance | TN | a level where sellers stopped a rise before | ceiling, wall, lid, overhead supply |
| key support / key resistance | TN | the support or resistance the plan uses | major support, strong resistance |
| entry zone | TN | the area where the plan buys, named by what happens there | buy zone, accumulation area, sweet spot |
| entry gate | TN | a condition that must be true before the plan buys | trigger (for an entry gate), setup (for an entry gate), green light |
| stop | TN | the condition where the plan exits — a level that breaks, named by what happens there (rule 2) | stop-loss, SL, cut-loss, exit level, uncle point |
| trailing stop | TN | a stop that moves up as the price rises | moving stop, chandelier |
| target | TN | the level where the plan takes profit, named by what happens there | price target, PT, take-profit, TP, objective price |
| analyst price target | TN | a price that an analyst publishes for the stock | target (alone), PT |
| range | TN | the area between support and resistance where the price moves | channel (unless the inputs name a channel), box (for a range), trading band |
| consolidation range | TN | a range that forms after a trend, before the next move | base (for a pattern), flag (for a pattern), coil |
| all-time high | TN | the highest price the stock ever reached | ATH, record (for a price) |
| 52-week high / 52-week low | TN | the highest (or lowest) price in the last 52 weeks | yearly high, 1-year high |
| open / high / low | TN | the first, the highest and the lowest price of a session | opening print, top (for a session high), bottom (for a session) |
| opening range | TN | the high and low of the first stated minutes of a session | ORB, first bar |
| previous close | TN | the close of the last session | prior settle, yesterday's print |

### Price movement

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| uptrend / downtrend | TN | a series of higher (or lower) highs and lows | bull run, rally (for a trend), sell-off (for a trend) |
| higher high / higher low | TN | a high (or low) above the previous one | HH, HL |
| lower high / lower low | TN | a high (or low) below the previous one | LH, LL |
| pullback | TN | a fall in price inside an uptrend | dip, retracement, retrace, pullin |
| rebound | TN | a rise in price after a fall | bounce, relief rally, dead cat bounce |
| reversal | TN | a change of a trend to the opposite direction | turn (for a reversal), flip (for a reversal), U-turn |
| breakout | TN | a session close above resistance | pop, rip, breakaway, liftoff |
| breakdown | TN | a session close below support | collapse (for a breakdown), crack, flush, rollover |
| failed breakout / failed breakdown | TN | a breakout (or breakdown) that the next sessions reverse | bull trap, bear trap, fakeout, head fake |
| gap | TN | an open far from the previous close | jump (for a gap), air pocket, gap-and-go |
| gap fill | TN | a move back to the previous close after a gap | gap close, fill (for a gap) |
| close | TN | the last price of a session | settle, print (for a price), EOD |
| session | TN | one trading day | day (when the trading day is meant), bar (for a session), candle |
| momentum | TN | the speed of the move — say it extends or rolls over | thrust, oomph |
| participation | TN | how much trading backs a move — it confirms or fades | volume (for participation), turnover (for participation) |
| accumulation / distribution | TN | steady buying (or selling) by large holders, seen in participation | loading (for accumulation), dumping |
| money flow | TN | the net flow of money into or out of the stock | flows (alone), fund flow (for one stock) |
| stretched / washed out | TN | a move far above (or below) its usual range, likely to revert | overbought (for this meaning), oversold (for this meaning) |
| volatility | TN | how much the price moves — quiet, coiled, expanding or volatile; state realized or implied | vol (alone), swings (for volatility) |

### Actions — the technical verbs

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| buy | TV | open or increase a position | go long, load, pick up, pull the trigger, get in |
| sell | TV | decrease or close a position | dump, unload, offload, get out |
| sell short | TV | sell shares the plan does not own, to buy them back lower | short (as a verb), go short |
| cover | TV | buy back shares that the plan sold short | buy to close, flatten (for cover) |
| add | TV | buy more of a position that is open | top up, pyramid, average up |
| trim | TV | sell part of a position | pare, lighten, take some off, peel |
| exit | TV | sell all of a position | bail, dump, get out, flatten (for exit), close out |
| scale in / scale out | TV | buy (or sell) in steps, at stated conditions | leg in, ease in, peel off, ladder |
| hold | TV | of a level: the price stays on the same side of it at the close | survive (for a level) |
| keep | TV | keep a position and do not sell or add | sit tight, let it ride, diamond hands, hold (for a position) |
| stand aside | TV | keep capital in cash and do not buy | sit on hands, wait it out, stay flat |
| hedge | TV | reduce the risk of a position with a second trade | protect (for a hedge), insure (for a hedge) |
| commit | TV | put the stated % of capital into the market | deploy aggressively, go all in, size up |
| test | TV | move to a level and touch it | probe, tag (for a test), kiss |
| break | TV | close through a level | crack, slice through, lose (a level) |
| reclaim | TV | close above a level again after a close below it | retake, regain, recapture |
| confirm | TV | show with evidence that a scenario is true | validate (for confirm), prove out |
| fail | TV | not hold a breakout, a breakdown or a level | fizzle, roll over (for a level) |
| roll | TV | close an option and open the same type at a later expiration date or new strike | roll over, extend (for an option roll) |

### Market measures — what the report may name

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| relative strength | TN | the stock's return compared with a stated benchmark | RS (alone), outperformance (alone) |
| breadth | TN | how many stocks in an index rise compared with how many fall | participation (for breadth), internals |

### Do not name in the report (rule 6)

The engine downstream chooses the measurement from live data; name the
instrument and it has to stop and ask. Write the behaviour instead.

| Do not write | Write the behaviour instead |
|---|---|
| RSI, overbought, oversold, stochastic | the move is stretched / the move is washed out |
| MACD, MACD cross | momentum extends / momentum rolls over |
| ADX | the trend is strong / the trend is weak |
| moving average, SMA, EMA, 50-day, 200-day | the trend, or the level by what happens there ("the level buyers defended") |
| VWAP | where the session's buyers and sellers balanced |
| ATR, average true range | the usual daily move / volatility is quiet or expanding |
| relative volume, RVOL, volume above average, average volume | participation confirms the move / participation fades |
| Bollinger band, Bollinger | the move is stretched / volatility is coiled |
| fib, Fibonacci | the level by what happens there |

### Fundamentals

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| revenue | TN | the company's sales in a stated period | top line, sales (mixed with revenue) |
| earnings per share (EPS) | TN | net income per share in a stated period | bottom line, earnings (for EPS) |
| estimate | TN | the analysts' average forecast for a value | consensus (alone), whisper, the number |
| estimate revision | TN | a change of the estimate by analysts | revision (alone), re-rate (for estimates) |
| guidance | TN | the company's own forecast | outlook (for guidance), the guide |
| margin | TN | profit as % of revenue; state gross, operating or net | profitability (for a margin) |
| free cash flow | TN | cash from operations minus capital spending | FCF (before the first full use), cash generation |
| valuation | TN | the price compared with a stated value measure | multiple (alone), rich (for valuation), cheap (for valuation) |
| P/E ratio | TN | the price divided by EPS | multiple (alone), PE |
| PEG ratio | TN | the P/E ratio divided by the growth rate | growth-adjusted multiple |
| EV/EBITDA | TN | enterprise value divided by EBITDA | EBITDA multiple |
| debt-to-equity ratio | TN | total debt divided by shareholders' equity | leverage (alone), gearing |
| dividend | TN | cash the company pays to shareholders | payout (for a dividend), distribution (for a dividend) |
| ex-dividend date | TN | the first date a buyer does not get the next dividend | ex-date, ex |
| dividend yield | TN | the yearly dividend as % of the price | yield (alone, for a stock) |
| payout ratio | TN | dividends as % of earnings | coverage (for payout) |
| buyback | TN | the company buys its own shares | repurchase program (mixed), BB |

### Market and macro

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| market regime | TN | the market state that CA names (for example "risk-on regime") | market mood, backdrop, environment (for a market regime) |
| risk-on regime / risk-off regime | TN | a market state where buyers take more (or less) risk | risk appetite (for the market), risk-on (alone) |
| index | TN | a stated market index (S&P 500, Nasdaq 100) | the market (for an index), tape (for volume or an index) |
| benchmark | TN | the index or sector the stock is compared with | comp, bogey |
| sector | TN | the stated industry group (for example "semiconductors") | space (for a sector), group (for a sector), complex (for a sector) |
| sector rotation | TN | capital that moves from one sector to another | rotation (alone), flows (alone) |
| interest rate | TN | the stated rate (for example "the federal funds rate") | rates (alone), the Fed (for a rate) |
| Treasury yield | TN | the yield of a stated US Treasury bond (write "10-year Treasury yield") | yields (alone), the 10s |
| yield curve | TN | the difference between a long and a short Treasury yield; state which | the curve (alone) |
| inflation | TN | the change in prices; state CPI or PCE | price pressures, inflation print |
| FOMC meeting | TN | a dated Federal Reserve rate meeting | the Fed (for the meeting), Fed day |
| jobs report | TN | the monthly US employment report | NFP (before the first full use), payrolls |

### Positioning and flows

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| institutional flow | TN | buying or selling by funds and institutions | smart money, big money, whales |
| institutional ownership | TN | the share of stock that institutions own, as % | institutional sponsorship, holders (for ownership) |
| insider buying / insider selling | TN | stock that company officers buy (or sell) | insider activity (alone), cluster (for insider buying) |
| short interest | TN | the shares sold short, as % of float | shorts, short position (for the market) |
| days to cover | TN | short interest divided by average volume | DTC, cover days |
| float | TN | the shares that the public can trade | free float (mixed), tradeable shares |
| short squeeze | TN | a fast rise caused by short sellers who cover | squeeze (alone), gamma squeeze (unless stated) |
| options flow | TN | large or unusual option trades | unusual activity (alone), sweeps |
| put/call ratio | TN | put volume divided by call volume | PCR |
| analyst rating | TN | an analyst's published buy, hold or sell view | call (for a rating), upgrade (alone) |

### Plan and evidence

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| plan | TN | this report's position plan | strategy (for the report), play (for the plan), trade idea |
| campaign | TN | the plan's primary objective and its numbers | mission, initiative |
| scenario | TN | one possible path of the price, with a probability | case (for a scenario), outcome (for a path), branch (for a scenario) |
| thesis | TN | the reason the plan expects its scenario | idea (for the thesis), story (for the thesis), narrative (for the reason) |
| signal | TN | CA's call: strong buy, buy, hold, sell, strong sell | rating (for the signal), recommendation (for the signal), call |
| confidence | TN | how sure CA is of its signal | certainty (for confidence), conviction (for CA) |
| conviction | TN | how strongly the plan commits (the Z digit) | confidence (for the plan), certainty (for confidence) |
| horizon | TN | the expected time the plan runs, as a duration | deadline, expiry, timeframe (for the horizon) |
| probability | TN | how likely a scenario is, as % | odds, chance, likelihood |
| base rate | TN | how often an outcome happened before, as % | history (alone), track record |
| catalyst | TN | a dated event that can move the price | trigger event, driver (for an event) |
| earnings report | TN | the company's quarterly results | the print, print, the number, results day |
| event risk | TN | the risk that a dated event moves the price against the plan | binary event, binary, landmine |
| gap risk | TN | the risk that the price opens beyond the stop | overnight risk (alone) |
| observable | TN | a thing the evidence can show, that confirms or breaks a scenario | signal (for this meaning), tell (for an observable), clue (for an observable) |
| evidence | TN | the facts from the workers and the analysis | data points, color (for evidence) |
| R:R / reward-to-risk ratio | TN | the expected gain divided by the possible loss | risk/reward, RR, R/R |

### Options and volatility (X5 and X7 plans)

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| option, call, put | TN | the contract types | contract (alone), the calls |
| strike | TN | the price in the option contract | strike price (after the first use), K |
| expiration date | TN | the last date of an option contract | expiry, exp |
| days to expiration | TN | the sessions or days until the expiration date | DTE (before the first full use), tenor |
| premium | TN | the price paid for an option | cost (for an option), the option price, juice |
| in the money / at the money / out of the money | TN | where the strike is compared with the price | ITM, ATM, OTM |
| implied move | TN | the price move that option prices show for an event | expected move (unless quoted), straddle price |
| implied volatility (IV) | TN | the volatility that option prices show | vol, IV (before the first full use) |
| IV rank | TN | where IV is, as a percentile of its range | IV percentile (mix), IVR |
| realized volatility | TN | the volatility that the price showed | historical vol, HV |
| term structure | TN | implied volatility across expiration dates | vol curve, term (alone) |
| skew | TN | the difference in implied volatility between puts and calls | smile (for skew), put skew (unless stated) |
| delta | TN | how much the option price changes for a one-dollar move in the stock | hedge ratio (for options), deltas (as a count) |
| time decay | TN | the fall in an option's value as time passes (theta) | theta burn, decay (alone) |
| open interest | TN | the number of option contracts that are open | OI |
| spread | TN | a named option structure with two or more legs (state which) | combo, the structure |
| collar, straddle, strangle, iron condor | TN | the named option structures | combo, the structure |
| covered call | TN | a call sold against shares the plan holds | buy-write (mixed), overwrite |
| protective put | TN | a put bought to limit the loss on shares | married put (mixed), insurance (for a protective put) |
| assignment | TN | the option holder uses the option, so the plan must buy or sell shares | exercise (for the seller's side), getting called away |

### Pairs (X7 plans)

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| pair | TN | two stocks traded together, one bought and one sold short | pair trade (alone), stat arb |
| pair spread | TN | the price difference between the two stocks, after the hedge ratio | spread (alone, for a pair) |
| hedge ratio | TN | the number of shares sold short for each share bought | beta (for this meaning), ratio (alone) |
| z-score | TN | how far the pair spread is from its average, in standard deviations | sigma, std devs |
| correlation | TN | how closely two prices move together, from -1 to 1 | relationship (for correlation), co-movement |
| cointegration | TN | a stable long-term link between two prices | mean-reversion (for this meaning) |
| half-life | TN | the expected time for the pair spread to move halfway back to its average | reversion time |

### Time and sessions

| Preferred term | Type | Meaning in this report | Do not use |
|---|---|---|---|
| intraday | TN | inside one session | day-trade (for a period), in-session |
| pre-market / after-hours | TN | trading before the open (or after the close) | extended hours (alone), overnight session |
| overnight | TN | from one close to the next open | o/n |
| quarter | TN | a fiscal quarter of the company (write "Q3 FY27") | Q (alone), period (alone) |
| week / month | TN | calendar weeks or months, as a duration | wk, mo |

### Words to replace

| Do not write | Write |
|---|---|
| dry powder, ammo, firepower | cash |
| pull the trigger, get in | buy |
| load up, back up the truck | buy a large position |
| catch a falling knife | buy in a downtrend |
| buy the dip | buy on a pullback |
| chase | buy above the entry zone |
| double down | add to a losing position (and say why) |
| average down | add at a lower price than the average entry price |
| bag holder | the owner of a losing position |
| stopped out | the plan exited at the stop |
| cut losses | exit at the stop |
| lock in gains | sell to keep the gain |
| let it ride | keep the position |
| ripping / tanking | rises fast / falls fast |
| melt-up / pop | a fast rise |
| flush / capitulation | a fast fall with very heavy participation |
| bounce, dead cat bounce | a rebound (inside a downtrend) |
| bull trap / bear trap | a failed breakout / a failed breakdown |
| shake-out | a fast fall that reverses in the same session |
| choppy | fast moves in both directions, with no trend |
| sideways | in a range |
| to the moon, moonshot | a large rise (give the %) |
| dead money | a position that does not move |
| frothy, extended (for price) | stretched (describe what the move did) |
| cheap / expensive (valuation) | a low / high valuation (give the measure) |
| priced in | the price already shows it |
| sell the news | the price falls after the event |
| the print (earnings) | the earnings report |
| print (a price) | close |
| beat / miss (earnings) | a result above / below the estimate |
| guide up / guide down | raise / lower guidance |
| headwind / tailwind | a factor against / for the thesis |
| the tape, price action | price and participation / price movement |
| green day / red day | a session that closes up / down |
| loses the level | the level breaks |
| fade (a move) | sell into a rise / buy into a fall |
| bid up / sold off | rose / fell |
| risk-on / risk-off (as an adjective) | a risk-on regime / a risk-off regime |
| leg (of a move) | part |
| strength / weakness (alone) | a rise in price / a fall in price |
| downside | possible loss (as a noun); below the price (as an adjective) |
| upside | possible gain (as a noun); above the price (as an adjective) |
| post-print / pre-print | after the earnings report / before the earnings report |
| theta burn | time decay |
| juice (options) | premium |
| vol (alone) | implied volatility or realized volatility |
| FOMO | (do not use) |
| skin in the game | (do not use) |
| no-brainer, slam dunk | (do not use; state the evidence) |

---

## The report template — EXACT section layout

```markdown
# <TICKER> — <Signal> (<Confidence>)

<ONE PARAGRAPH — 3-5 sentences. The executive summary. What is the plan,
what thesis does it ride, what are its two bounds. Written as if the
reader has 10 seconds to understand what we're doing. No headers inside.>

## Thesis

<What is happening, why it is worth owning, and why THIS is the way to own
it. How firmly you hold it, and for how long. Two or three sentences — the
paragraph above is the summary; this is the argument.>

## Catalysts

<Dated lines, one per catalyst. EVERY DATE IN FULL, WITH ITS YEAR. The
engine that reads this will not guess, and an unstated date becomes a
question somebody has to stop and answer before the plan can run.>

- <event>, <YYYY-MM-DD> (<before / after the close>)
- <event>, <YYYY-MM-DD>

<⚠️ If the thesis turns on earnings, the ticker's OWN earnings report must be
here. A peer's report, the sector's or the market's never counts. If two of
its own reports fall inside the horizon, say which one each statement means.>

## What may be traded

<ONE LINE — shares only, or options admitted. Follows X from the envelope;
never widen it.>

## Stance

<ONE LINE — all caps, no trailing punctuation. The commit mode as a
headline. Examples: "COMMIT IN ONE DECISIVE STEP" / "BUILD PATIENTLY, IN
TRANCHES" / "STAND ASIDE INTO THE PRINT" / "COVERED CALLS ON STRENGTH".
Keyed to the campaign's staging+aggression, mapped from Z.>

## Primary campaign

**ID:** `<kebab-slug>` ← one-line, lowercase, dashes. Example: `nvda-pivot-accumulate`
**Name:** <short display name, up to ~60 chars>
**Objective:** <one sentence — what the plan is trying to achieve>
**Goal:** <ACCUMULATE | DEFEND | DISTRIBUTE | HEDGE | STAND_ASIDE | HARVEST | PROFIT_CAPTURE | HOLD_AND_COMPOUND | RESOLVE_OR_EJECT>

**Horizon:** <plain duration — "4 weeks" / "3 months" / "about 6 weeks">
**R:R floor:** <number — the minimum reward-to-risk ratio, e.g. `2.0`; the card shows it, TensorPit does not size on it>
**Risk posture:** <one phrase — `measured-active` | `patient` | `decisive` | `defensive`>

**Capital velocity:** <low>–<high>% of capital
**Staging:** <single | scaled | tranched>
**Aggression:** <patient | measured | decisive | immediate>

**Outcome:**
- Upside target: +<N>%   ← a move in the PRICE from the entry
- Max loss: -<N>%   ← a move in the PRICE from the entry

**End holding:** <stock | cash>
**Once holding:** <one sentence — how the plan behaves once it holds the position, compared with when it is flat>

<ONE PARAGRAPH — why these numbers. Which CA dimensions back the magnitudes,
which worker evidence back the pacing, how conviction (Z) shaped the staging.
This is the mandate's rationale, in the planner's voice.>

## Deployment

<ONE LINE — the deploy sentence, which is literal and the UI shows
verbatim. Example: "Deploy 15%–40% of capital today." Numbers must match
Primary campaign.Capital velocity.>

<ONE LINE — the entry gate, if the plan has one. Example: "Build only
after the reaction to the earnings report resolves a level." A plan with no single
gate omits this line; its first scenario carries the gate instead.>

## Plan bullets

<1-3 BULLETS — each one line, under 80 characters, something a reader
could ACT ON. These are the card's action block, shown directly under the
stance, so write them for a reader who sees nothing else. Order them by
what matters most on THIS campaign — the gate, the risk, the pacing —
and lead with a verb.

Every bullet must restate something already decided above: the deployment
line, an entry gate, a stop, a magnitude, a horizon. Do not introduce a
number here that appears nowhere else in the report.

Vary the opening verbs. Three bullets that all start "Deploy…" read as a
template; "Build…", "Trim…", "Stand aside…", "Keep…", "Scale…" are the
register. Never pad to three — two true lines beat three with filler.

- <bullet>
- <bullet>
- <bullet>>

## Scenarios

<One subsection per scenario, in probability order (highest first).
MINIMUM: bull + base + bear, plus the one that KILLS the idea (3).
MAXIMUM: 6 — more than that and no two are really different.

⛔ THE SCENARIO PROBABILITIES MUST ADD TO 100%.

Give each a different DRIVING FORCE — a technical story, an earnings story,
a macro story. Three sizes of the same move is one scenario written three
times.>

### Scenario: <scenario-key> — <display name>

**Bias:** <upside | neutral | downside>
**Probability:** <N>%
**Driving force:** <one of: technical | fundamental | macro | sentiment | positioning | event | peer>

<ONE PARAGRAPH — context: what sets this scenario up, what the market
would be doing in it.>

**Summary:** <2-3 sentence summary of how it plays out — the opening, the
mid-path, the resolution.>

**Paths:** <2 to 4. A PATH is one way this scenario plays out. Each needs
what you would RECOGNISE it by, and which of six outcomes it is:

  `accelerates` · `progresses` · `stalls` · `retraces` · `whipsaws` · `fails`

The outcome says what the path MEANS for its own scenario — never what to
do about it. The engine decides that, weighing every path's odds.

⛔ Every path except a `stalls` one needs recognition conditions. Time alone
moves a stall's odds; everything else has to be visible in price and participation.
⛔ A `fails` path NAMES ITS SUCCESSOR — which of your other scenarios takes
over. That is what hands the campaign on instead of ending it.
⚠️ The paths' probabilities add to 100% WITHIN the scenario.>

- *<path name> (~<N>%)* — <what you would see while it is happening>. <OUTCOME>.
- *<path name> (~<N>%)* — <what you would see>. <OUTCOME>.
- *<path name> (~<N>%)* — <what you would see>. <OUTCOME>; <scenario-key> takes over.

**Thesis observables:** <what would make you believe this scenario is TRUE
when you see them>
**Confirming observables:** <signals you'd watch for as the plan runs,
confirming the setup is still alive>
**Invalidating observables:** <what would break the thesis — observable
signals, not price targets alone>

## Mandate

<What this campaign is FOR, in one or two sentences.>

<And the conditions under which it has FAILED even though it has not lost
money yet — "price stops giving clean entries, and each attempt dies between
the objective and the stop" is a real failure criterion. This is the section
authors leave out and the one that saves money: the plan is reviewed against
it on a schedule, not only when it is down.>

## Market pulse

<What you are assuming without watching it. At minimum the RESIDUAL.>

- **Assumption:** <something you are leaning on — "financial conditions stay accommodative">
- **Unknown:** <a dated event you cannot handicap>
- **Residual:** <"price does something none of these scenarios describes">
- **Opportunity:** <OPTIONAL — a condition under which the plan should do MORE than planned>

<A report with no pulse believes nothing outside its own scenarios can
matter.>

## Execution plan

**Patience:** <normal | high | low>
**Reentry:** <allowed | once | forbidden>
**Exit discipline:** <full_book | partial | runner>
**Invalidation urgency:** <confirmed | tight | wide>

<ONE PARAGRAPH on the overall execution style. How this plan behaves
after entry: does it add on strength, trim on reaches, exit decisively,
re-enter on reset. Shaped by Z (conviction) and the risk posture.>

## Action steps

<One subsection per scenario — ONE action step per scenario, in the same
order the Scenarios section used. If a scenario has NO action step (sit-
out branch), omit its block.>

### Step: <scenario-key> — <imperative title, under 60 chars>

**When:** <pre-entry | on-signal | during | exit>

<ONE OR TWO SENTENCES — what to do in this step, and why. Grounded in
the scenario's paths and the campaign's staging. Written as instruction,
not reflection.>

## Rules

<Simple condition → action pairs, one per line. The arrow `→` is the
separator the extraction agent uses to split condition from action — use
it exactly. Rules carry the loop logic that governs the position
mechanically, outside the discretionary action-step prose.>

- **If <condition>** → <action>
- **If <condition>** → <action>
...

## Why this plan

Six fixed sub-fields explain the plan to the reader, each in one or two
sentences. OMIT a sub-field when the plan genuinely doesn't carry that
answer — a missing sub-field is better than a padded one.

**Cash:** <what uncommitted money is doing — waiting on what, parked where,
how long>

**Stop:** <what ends the position and WHY — structural (a level reclaims
and holds) not just a drawdown number>

**Gates:** <what has to be true before any buying — written as evidence or
condition, not price alone>

**Posture:** <the risk posture, the R:R floor, how decisively the plan
acts. One sentence that reads straight from the staging+aggression combo>

**Horizon:** <what the duration means — "typically about X" or "estimate
of pacing, not deadline". Never a countdown>

**Velocity:** <how fast the capital reaches the market and why. Reads
the capital-velocity band as % of capital, NOT days>
```

---

## Section invariants (what the extraction agent will key on)

ProStocks' extraction agent reads your report and emits the UI contract fields
of the position card. It uses the following
invariants:

| Section heading | What the extractor expects |
|---|---|
| `# <TICKER> — ...` + the one paragraph under it | `report.exec` (executive summary) |
| `## Stance` | `stance` (one line, upper-case) |
| `## Primary campaign` + `**ID:**`, `**Name:**`, `**Objective:**` | `primary_campaign.{id, name, objective}` |
| Primary campaign + `**Horizon:**`, `**R:R floor:**`, `**Risk posture:**` | `max_duration`, `rr_floor`, `risk_posture` |
| Primary campaign + `**Capital velocity:**` line (with `X–Y%` pattern) | `capital_velocity.{low, high}` |
| Primary campaign + `**Staging:**` and `**Aggression:**` | `capital_velocity.staging`, `.aggression` |
| Primary campaign + `**Outcome:**` with `Upside target: +N%` / `Max loss: -N%` | `outcome.{upsidePct, downsidePct}` |
| `## Deployment` first line | `deployment.line` + `deployment.recommendedPct`/`targetPct` (parsed from its `X–Y%`) |
| `## Plan bullets` `-` lines | `plan_bullets[]` (REQUIRED — one per bullet, max 3) |
| `## Thesis`, `## Catalysts`, `## What may be traded`, `## Mandate`, `## Market pulse` | no card field — TensorPit's compiler reads every section, and a plan without these five costs a round of questions before it can run |
| `## Scenarios` + `### Scenario:` blocks | `scenarios[]` (one per block) |
| Each scenario's `**Bias:**`, `**Probability:**`, `**Driving force:**` | per-scenario fields |
| Each scenario's `**Paths:**` lines | the scenario's trajectories — one per line, each carrying its recognition and its outcome |
| Each scenario's `**Thesis/Confirming/Invalidating observables:**` | the three observable lists |
| `## Execution plan` sub-fields | `execution_intent.{aggression, staging, exitDiscipline, invalidationUrgency, reentry, patience}` |
| `## Action steps` + `### Step:` blocks | `action_steps[]` with `title`, `when`, `detail` |
| `## Rules` arrow lines | `rules[]` (one per `→` line) |
| `## Why this plan` sub-fields `Cash`, `Stop`, `Gates`, `Posture`, `Horizon`, `Velocity` | `module_reasoning.{cash, stop, gates, posture, horizon, velocity}` |

Keep section names exact, keep the sub-field names exact, and the extraction
is bulletproof. Rename a section and the UI loses that card neuron for the
whole run.

## Vocabulary — enum values the UI needs verbatim

Pass these literal strings so the extraction agent doesn't have to guess:

- **Goal:** `ACCUMULATE`, `DEFEND`, `DISTRIBUTE`, `HEDGE`, `STAND_ASIDE`, `HARVEST`, `PROFIT_CAPTURE`, `HOLD_AND_COMPOUND`, `RESOLVE_OR_EJECT`
- **Bias:** `upside`, `neutral`, `downside`
- **Tone:** `pos`, `neg`, `mixed`
- **Staging:** `single`, `scaled`, `tranched`
- **Aggression:** `patient`, `measured`, `decisive`, `immediate`
- **Exit discipline:** `full_book`, `partial`, `runner`
- **Invalidation urgency:** `confirmed`, `tight`, `wide`
- **Reentry:** `allowed`, `once`, `forbidden`
- **Patience:** `normal`, `high`, `low`
- **Path outcome:** `accelerates`, `progresses`, `stalls`, `retraces`, `whipsaws`, `fails`
- **Position state:** `in_cash`, `partially_deployed`, `fully_deployed`
- **Terminal state** (`**End holding:**`): `stock`, `cash`

These tags belong on their own `**Bias:**`-style lines, verbatim. In the
prose, use the word bank's preferred terms — not synonyms (writing standard,
rule 1).

---

## Planning — from the run settings to the card

### Z (conviction) → staging + aggression mapping

Your Z envelope digit tells you how to commit. The commit mode drives
`stance`, `capital_velocity.staging` and `capital_velocity.aggression`.

| Z | Band | Typical staging | Typical aggression | Capital velocity band |
|---|---|---|---|---|
| 9 | very high | single | decisive or immediate | 80–100% |
| 7-8 | high | single or scaled | decisive | 60–90% |
| 5-6 | medium | scaled | measured | 40–70% |
| 3-4 | low-medium | tranched | measured or patient | 20–50% |
| 1-2 | low | tranched | patient | 10–30% |

These are DEFAULTS, not laws. A low-conviction read of a late-stage bull
breakout can still be `single/decisive` if the risk is bounded. A high-
conviction thesis under heavy event risk (Z8 + earnings in 7 days) can
still be `tranched/patient` because the TIMING demands it. Explain in the
prose when you break the default.

### Horizon (Y) → duration guidance

| Y | Typical campaign duration |
|---|---|
| 1 | intraday — hours |
| 3 | short-term — 1-2 weeks |
| 5 | mid-term — 1-3 months |
| 7 | long-term fundamental — 6-12 months |
| 9 | structural — multi-year |

Write durations as prose ("about 4 weeks", "typically 2-3 months"), never
as a date. The CAMPAIGN ends when its thesis resolves or breaks.

### X (instruments) — what you may use

- **X3** — shares only. No options, no futures. Even if the thesis is
  short-dated, the plan is in shares.
- **X5** — shares + options. Use options when they are the right vehicle
  for the thesis (defined risk, convexity, income). Prefer shares for
  core; options for specific expression.
- **X7** — shares + options + other. Pairs, hedges, cross-asset. Rare;
  demands justification in the prose.

Do NOT reach for options or complex structures on an X3 stance.

### Degradation

If you lack a specific input:

- **No clear entry gate** → omit the gate line in Deployment. The first
  scenario's path carries it instead.
- **No invalidating observables** → omit that line in the scenario.
- **No obvious secondary scenarios** → the minimum is 3 (bull/base/bear);
  write only what the evidence actually supports.
- **A price feels necessary to make the point** → say the BEHAVIOUR instead.
  "The level buyers defended through the summer is holding" carries the plan;
  "price > $94.50" does not: a hard price goes stale as the stock moves (rule 2).
- **CA signal is HOLD with low confidence** → write a STAND-ASIDE
  campaign; the stance is "STAND ASIDE FOR NOW", capital velocity is 0-0,
  staging is `single`, aggression is `patient`, no scenarios need
  paths.

A section that can't be grounded is OMITTED, not padded. The UI handles
a missing neuron cleanly; a fabricated one is a lie it will show.

### Anti-patterns — do NOT write these

❌ **A dashboard of indicators.** "RSI 46, MACD neutral, ADX 15, 20-SMA
sloping, 50-SMA holding..." This is an evidence dump, not a plan.

❌ **Bullet-list of everything.** The report is prose with structured
sub-items where the UI needs them, not a markdown list top-to-bottom.

❌ **A JSON object dressed in markdown.** `"id": "x"` outside of a
`**ID:** x` line, `{...}` blocks, serialised arrays — all signs you're
authoring the old directive. The new output is prose.

❌ **"The campaign will yield 18% over 4 weeks."** No promises. "Aiming
for +18% over about 4 weeks" is right; "will" is wrong.

❌ **A price with no reason.** A level is named by what happens there. A
price appears only when that exact price matters, with the reason in the same
sentence (rule 2), and it comes from the inputs, never invented (rule 5).

❌ **A countdown.** "6 weeks remaining", "expires 2026-11-27", "by month
end". The horizon is pacing — the plan ends when its thesis resolves,
not on a date.

❌ **A stance that doesn't match the staging.** "COMMIT IN ONE DECISIVE
STEP" on a `tranched/patient` plan. Stance IS the human headline for the
staging+aggression combo — match them.

---

## Example — short end-to-end

⚠️ Read how it names things. Every level in this example is described by what
HAPPENS there — "the level institutional flow has defended", "the old range
high" — and never by its number. That is rule 2, and the example is the part
of this document a writer copies most faithfully. It is also written in
ASD-STE100, and it passes the check PM runs on every report.

```markdown
# NVDA — Strong Buy (High Confidence)

NVIDIA is in a routine pullback inside an intact institutional uptrend. The
campaign accumulates at the level that institutional flow defended on each
test. It aims for a return to the recent high in about four weeks. It exits if
that level breaks and stays broken. High conviction (Z9) and a cheap PEG are
the reasons to commit in one decisive step.

## Thesis

The uptrend is institutional, not retail: buyers defended the same level on
three separate pullbacks, each with expanding participation. This pullback is
routine positioning before the earnings report, not a change of view. A cheap
PEG against 65.5% revenue growth shows that the market does not pay a premium
for the growth. Conviction is firm, and the horizon is about four weeks: the
time the base took to reclaim on each previous test.

## Catalysts

- Q3 earnings, 2026-11-04 (after the close)
- December index review, 2026-12-12

## What may be traded

Shares only.

## Stance

COMMIT IN ONE DECISIVE STEP

## Primary campaign

**ID:** `nvda-pivot-accumulate`
**Name:** Accumulate NVDA at the defended pivot
**Objective:** Buy when the defended level holds; take profit near the recent high, or exit if the thesis breaks.
**Goal:** ACCUMULATE

**Horizon:** about 4 weeks
**R:R floor:** 2.0
**Risk posture:** measured-active

**Capital velocity:** 70–100% of capital
**Staging:** single
**Aggression:** decisive

**Outcome:**
- Upside target: +12%
- Max loss: -6%

**End holding:** stock
**Once holding:** The plan keeps the core position and does not add above the entry zone.

A large buyback authorisation and steady index demand absorb the supply at the
defended level. This makes the defended level structural support for the
entry. A PEG of 0.23 against 65.5% revenue growth makes the entry cheap, even
after the pullback. Z9 and High CA confidence justify the decision to commit in
one step. The invalidation is close to the entry, so a decisive position size
is correct.

## Deployment

Deploy 70%–100% of capital today.
Enter when the defended level holds: a session close above it after an intraday test.

## Plan bullets

- Build the core position when a session closes above the defended level.
- Exit when that level breaks and stays broken: the thesis breaks with it.
- Aim for +12% in about four weeks, and exit at -6%.

## Scenarios

### Scenario: pivot-reclaim — The defended level holds and the base reclaims

**Bias:** upside
**Probability:** 55%
**Driving force:** positioning

Institutional buyers defended this level in three recent sessions. When the
level holds, participation expands again and the price reclaims the base above
it in two to three sessions.

**Summary:** Enter when the level holds, and trim a third at the first
resistance. Sell most of the rest near the recent high, and keep a runner. The
plan commits most of its capital in one decisive step, because the level is
structural and the invalidation is close.

**Paths:**
- *Clean reclaim (~50%)* — the level holds on the test, participation expands, and the base above it reclaims in the first week. The thesis PROGRESSES.
- *Fast rise before the earnings report (~20%)* — the price rises straight to the recent high without a pause, as buyers move in before the date. ACCELERATES.
- *Reclaim then drift (~30%)* — the base reclaims, but participation thins and the price stays flat until the earnings report. STALLS.

**Thesis observables:** institutional flow shows net accumulation at the defended level; buyers defended it on each of the last three tests
**Confirming observables:** participation in each session stays above its recent average; the price where each session's buyers and sellers balanced rises session over session
**Invalidating observables:** two consecutive closes below the defended level, OR participation fades on the reclaim attempt

### Scenario: slow-grind — The level holds but mean-reversion stalls

**Bias:** neutral
**Probability:** 30%
**Driving force:** technical

The level holds, but neither side commits. The price stays in a range between
the defended level and the old range high for three to four weeks. The range
continues until a dated catalyst decides the direction.

**Summary:** Enter when the level holds, and keep the position with a wider
tolerance. Trim at the old range high, and add back when the price tests the
defended level again. Neither side makes a decisive move.

**Paths:**
- *Quiet range (~60%)* — the lows rise inside the range, realized volatility compresses, and neither side commits until the earnings report. STALLS.
- *Range with a false break (~25%)* — one push above the old range high fails and comes straight back into the range. WHIPSAWS.
- *Range gives way late (~15%)* — the defended level breaks in the week before the earnings report, and the price does not reclaim it. FAILS; base-fail takes over.

**Thesis observables:** the realized range compresses over the last 20 sessions; volatility is quiet
**Confirming observables:** the lows rise inside the range
**Invalidating observables:** the range breaks decisively in either direction, with expanding participation

### Scenario: base-fail — The defended level breaks and stays broken

**Bias:** downside
**Probability:** 15%
**Driving force:** macro

A macro shock or a disappointment in the semiconductor sector drives the price
below the defended level and keeps it there. The thesis breaks. This is an exit
scenario, NOT a scenario to buy more as the price falls.

**Summary:** Scale out on the first close below the level. Exit fully when the
next support breaks. Do not re-enter until the price reclaims the level with
expanding participation.

**Paths:**
- *Clean breakdown (~55%)* — the level breaks with expanding participation, and the price does not reclaim it in three sessions. The thesis FAILS; no scenario takes over, and the campaign ends with no position.
- *Break and partial recovery (~45%)* — the level breaks, then the price recovers most of the fall but does not reclaim the level. RETRACES.

**Invalidating observables:** the price reclaims the level with expanding participation AND the macro cause resolves

## Mandate

Accumulate a core position at the level that institutional flow keeps
defending. Keep the position through the earnings report until the base
reclaims.

The campaign has failed, whatever its profit or loss, if buyers stop defending
the level on each test. It has also failed if three consecutive reclaim
attempts fail between the gate and the objective. Either signal shows that the
institutional demand under the thesis has gone. The plan exits before the price
makes that obvious.

## Market pulse

- **Assumption:** financial conditions stay accommodative, so large-cap growth stocks keep their valuation.
- **Unknown:** the December index review on 2026-12-12; its direction is not known until the list of constituents is published.
- **Residual:** the price does something that none of these three scenarios describes, because of news from outside the company.
- **Opportunity:** if the base reclaims BEFORE the earnings report and not after it, the thesis is confirmed earlier and at a lower cost than planned.

## Execution plan

**Patience:** normal
**Reentry:** once
**Exit discipline:** partial
**Invalidation urgency:** confirmed

The plan commits once and keeps the position. When the price rises to the first
resistance, the plan trims a third. Near the recent high it sells most of the
rest, but it keeps a runner in case the rise continues. When the price falls,
the plan waits for two closes below the level before it exits. One bad bar does
not start the exit. If the plan exits at the stop, it may re-enter once, when
the price reclaims the level and institutional flow confirms it.

## Action steps

### Step: pivot-reclaim — Commit while the defended level holds

**When:** on-signal

Commit the planned capital when a session closes above the defended level after
an intraday test of it. Buyers defend the level, and the plan must own the
shares when the base reclaims.

### Step: slow-grind — Trim at the range high, do not buy the top

**When:** during

If the move stalls at the old range high, trim a tenth of the position. Add it
back when the price returns to the defended level. The plan keeps the position
through the range, because the structural thesis is intact.

### Step: base-fail — Scale out as the level breaks

**When:** exit

On the first daily close below the defended level, sell half the position. On
the second consecutive close below it, exit the balance. Re-enter once at most,
when the price reclaims the level and institutional flow confirms it.

## Rules

- **If the accumulate objective is met** → sell most of the position in planned parts near the recent high, and keep a runner while the base holds
- **If the defended level breaks on two consecutive closes** → exit as the loss plan says, and allow one reentry on a confirmed reclaim
- **If the base above the level reclaims with expanding participation** → the base is confirmed; keep the full position until the price reaches the recent high
- **If participation fades on the reclaim attempt** → the base is not confirmed; reduce the position to the recommended minimum and wait

## Why this plan

**Cash:** The plan commits 70–100% of capital on the signal, because the level is structural and the invalidation is close. Cash earns nothing while the structural pivot plays out. The plan must own the position for that move, and not wait for a better entry that may not come.

**Stop:** The stop is two consecutive closes below the defended level. It is structural, not a drawdown number, because a one-bar wick does not break the thesis. The level that justified the entry is the level that invalidates it.

**Gates:** The gate is a session close above the defended level after an intraday test of it. It is a pattern, not a target: the level holds and the price reclaims it, as it does when institutions defend a level.

**Posture:** The risk posture is measured-active, with an R:R floor of 2.0. The plan acts decisively on the signal and patiently at the stop. It does not buy above the entry zone, and it does not sell on a single red bar.

**Horizon:** The horizon is typically about four weeks. It is an estimate of pacing, not a fixed end date. The campaign ends when the base reclaims or fails, not on a calendar date.

**Velocity:** The plan deploys 70–100% of capital today. The pace follows from Z9 and the close structural invalidation. Most of the capital goes in at once, because waiting adds no information after the gate confirms.
```

---

## Filing the report

**Submit it** — `submit_report` on the MCP connector, or `POST /api/v1/reports` with
`{ticker?, report, title?, author?}` (leave `ticker` out and the compiler names it from the report).
TensorPit checks it against this discipline, **generates the `strategy_id`** and queues it for the
compiler. ⛔ You never choose that id; it comes back to you, and everything about the strategy is
keyed on it afterwards — the compilation, the plan, the campaign. Quote it in any conversation
about this strategy.

Submitting answers with **warnings** where the discipline is thin — a scenario with no paths, an
event with no date, an indicator where the market should be. They do not block: the report is
stored and the compiler is the strict step. Read them and fix what they name before a model is
ever paid to read it; that is the cheapest round you will ever save.

⚠️ **The same text again is the same report.** Re-submitting bytes you already filed answers
`status: duplicate` with the `strategy_id` already issued, where it stands (`seenBefore`), and a
question to put to the person (`ask`): "I've seen this report already … Do you want to compile it
again?". Yes is the same submit with `compile_again: true` — a NEW strategy from the same text,
every question asked afresh. A pipeline with nobody to ask carries on with the earlier one.

Reports can also be **dropped as files** (`<TICKER>/<strategy_id>.md` in the folder TensorPit
polls, the id in the filename) — the same pipeline reads both.

⛔ **One strategy, many revisions — then frozen.** A report submitted over the connector or REST is
corrected under its id (`revise_report`, the whole text each time; `refine_report` for one
instruction on top) until you finalize it (`finalize_report`); re-submitting changed text is a NEW
strategy with a new id. A file dropped in the folder is compiled once: a changed view is a new file
under a new id. ⚠️ Until it is finalized, a submitted report is kept for 7 days after it was last
read or revised; one nobody finalizes is gone after that.

The ticker in the report must be the ticker you file it under.

## What happens after you file

1. The compiler reads the report and builds the campaign, correcting its own output over a few
   rounds against TensorPit's rules. Poll `get_compilation { strategy_id }` for where it stands, or
   loop on `watch_compilation { strategy_id, cursor? }` to follow it as it runs (each round, the
   checks, the outcome). `get_compilation_rounds` keeps every model call it made;
   `list_compilations` lists yours.
2. If it cannot settle something — most often a date you did not state — a SUBMITTED report comes
   back to you as `corrections_required`, with `TP-Q-REPORT-*` issues: answer them in a revision. A
   `TP-Q-REPORT-CHOICE` means the report states an entry or a stop the campaign would run
   differently (an entry style its stance does not offer, a stop on the side it never trades):
   restate it in the revision as the research means it. A FILE report asks the **account owner** in
   plain language; their answer is appended and the compile resumes.
3. The campaign appears in TensorPit, where its owner can simulate it on its probable futures.
4. ⛔ **Going live is the account owner's decision.** Nothing you file starts trading.

---

## Before you file — the checklist

- [ ] One ticker, one thesis, and the ticker matches the filename
- [ ] Every required section is present, spelled exactly as the template spells it
- [ ] 3–6 scenarios, with **probabilities adding to 100%**, and at least one upside and one downside
- [ ] Each scenario has a different **driving force**, not a different size of the same move
- [ ] 2–4 paths per scenario, each with **what you would see** and one of the six outcomes
- [ ] Path probabilities add to 100% within each scenario
- [ ] Every non-`stalls` path has recognition conditions; each `fails` path names its successor by its key
- [ ] Every catalyst has a **full date with its year**, stated in a sentence
- [ ] The ticker's **own** earnings report is listed, if the thesis turns on earnings
- [ ] Any relative-strength claim names the sector or peer group
- [ ] Confirming evidence is stated **separately** from what triggers the scenario
- [ ] Upside target and max loss, as **percentage moves in the price** from the entry
- [ ] The Stance headline matches the staging and the aggression; no campaign-level word in a path
- [ ] Failure criteria that do not depend on losing money
- [ ] A market pulse with at least the residual
- [ ] **No** indicator names, share counts or strikes; a hard price only with the reason it matters
- [ ] Every claim sits in **one quotable sentence**
- [ ] Every sentence follows the ASD-STE100 writing standard, and every trading term comes from the word bank
