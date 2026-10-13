---
name: finstory-scroll-story
description: Builds an interactive scroll story inside finstory (pinned chapters, step-by-step charts, KPI tiles, bridges, a budget/last-year switch, a Basis switch that shows what each step rests on, an appendix, dark mode and a phone layout) through the finstory connector. Claude agrees an outline from the user's request, the data, the company's memory and last period's story; finstory drafts it without AI and lists the facts that stand out; Claude asks the user about them, records the answers, and writes the words chapter by chapter, marking every claim as data, an answer or an assumption. finstory fills in and sources every number and refuses typed ones. Use whenever the user wants a finstory scroll story, a monthly or board story, an interactive, web or scrolly version of a finstory story, board pack or month-end narrative, "the 3STEP format", or a story page with a source on every number, even if they only paste a finstory story link or name a story.
---

# finstory scroll story

You build a scroll story straight into the user's finstory workspace. Each chapter is one argument: a pinned panel
with a chart on the right, steps down the left, KPI tiles above the chart. finstory draws it with its own engine and
keeps it private until a power user or admin publishes it.

**The one rule: you never write a number.** You read the data to understand it, then you write words and
references. finstory reads every reference as the saving user, fills it in, formats it the way the app does, and
stores a receipt per number. A digit typed into text, or a number typed into chart data, is refused, and the error
names the chapter and field. The one exception is the user's own numbers: kept in their answers, and written out
in the steps that cite them (section 6).

**Trust and memory come first.** A CFO must be able to tell, for every step, whether it comes from the data, from
something the user told you, or from your own reading. And what the user tells you this month is kept with the story,
so next month starts from it.

| file | what it is | read it |
|---|---|---|
| `references/reference-format.md` | sources, figures, placeholders, calculations, series, bridges, answers, basis, evidence, the draft | before the first save |
| `references/spec.md` | every field of a chapter, visual and step (the shape finstory draws) | when you need a field |
| `references/visuals.md` | which visual for which data shape | before planning the outline |
| `references/writing.md` | headlines, steps, claims you can defend, stale commentary | before writing |
| `examples/chapter.json` | one complete chapter by reference (invented data) | before the first chapter |

The tool notes are the server's own statement of the format: call `company_get_context(job: "build_story")` once and
read its `tool_notes` (SCROLL STORIES, PERIOD AND MEMORY, EVIDENCE, DRAFT) and `company_profile` (the company's own
background, written by its admins). If the tool notes and this skill disagree, the tool notes win.

Everything about the story lives in finstory. Keep no copy of its data, answers or outline in artifacts, files or
notes of your own.

## 1 · Gather what the outline needs

Four inputs decide the chapters. Gather them before proposing anything, in one round of calls where you can:

1. **The request**: the period, the audience (board, management team, owner), anything the user wants covered.
2. **The data**: `report_list` for the story-ready reports; `company_get_context(job: "analyse")` for the accounts,
   scenarios and dimensions behind `analysis_query` slices. Note what is *not* there: no budget, no classes or
   locations, no balance sheet.
3. **Memory**: `company_profile` (what the company sells, its segments and seasonality, KPI definitions, recurring
   drivers, the words the board uses).
4. **Last period's story**: `story_list` shows each scroll story's period. `story_get` on the latest earlier one
   returns its outline: its chapters and datasets, and `answers`, what the user explained last time.

## 2 · Propose the outline

An outline is the chapter list before any words: per chapter an `id`, a working `title` (words only, no figures), the
finding it argues, and its `datasets` (sources, reference-format.md section 1), one per angle (section 3). It is
bespoke: start from last period's chapters when there are any, then adjust to this month's request, data and memory.

- **Chapters follow findings, not reports.** Read the story-ready reports first (`report_preview_values` with
  `dataset: true`, all in one message) and find what the period is about: what moved, what explains it, what it does
  to the year and to cash. Each finding is a chapter. One report can feed several chapters, one chapter several
  reports.
- **Use the workspace's analysis reports.** Bridges, mix, conversion, cost, working-capital and forecast reports carry
  the angles a P&L alone does not; a slice (`analysis_query`) fills an angle no report shows.
- **An existing finstory story** the user names (`story_get`) is a ready outline: each content page becomes a
  chapter, and pages that make one argument merge into one chapter with several visuals.

Guidelines, not a template:
- **Monthly management or board story**: the headline month; revenue against budget and last year, by the dimension
  the company manages by; gross margin; costs; EBITDA or profit bridge; cash; the outlook (the year as forecast and,
  where the data holds it, next year against management's outlook); a detail or appendix table.
- **Small business, no budget** (typically QuickBooks): the headline month; revenue against last year, by customer and
  product where the books hold them; gross margin and the top cost movers; balance sheet, cash and working capital;
  class or location mix only if tracked; a data-quality chapter for whatever the evidence flags. Compare with last
  year and the prior month; never invent a budget.
- A monthly trend slice of a chapter's main line (`analysis_query` `trend`, 12 months) lets finstory say how unusual
  this month's movement is for that line. Add one where a chapter's finding is a movement.
- Length follows the scope: a flash update can be five chapters, a full board pack twelve to fifteen. A chapter
  without data is left out, never shown empty.

Show the outline to the user as a short numbered list (per chapter its title, its finding in a line and its angles)
and adjust it once. Don't read figures out to them at this stage.

## 3 · Design each chapter: one finding, seen from the angles that prove it

A chapter argues one finding, its headline. Its visuals are the angles that prove it, and its steps walk the reader
through them. The angles:

| angle | shows | visual |
|---|---|---|
| level | this period against budget and last year | `columns` with `delta`, KPI tiles |
| trend | the same measure month by month, so the reader sees whether this period is normal | `bars` with a budget `marker`; `line` for a ratio |
| breakdown | which items made it: channel, product, store, cost line | `hbars` variance, or with a `compare` tick |
| ratio | what each unit keeps or costs: a margin, a conversion, fees as a share of sales, days | `line` or `columns` |
| bridge | from the comparison to the result, leg by leg | `waterfall`, one leg revealed per step |
| consequence | what it does to the year, to cash or to capital: year to date, forecast, net debt | `columns`, `bars`, `line` |

Mix two kinds of chapter through the story, roughly half each:
- **One chart, walked part by part.** A bridge, a variance list or a monthly trend carries the whole finding; each
  step highlights one leg, row or stretch of months (`reveal`, `rows`, `cats`) and says what it adds.
- **One issue, two or three angles.** "Sales up, margin down": the month against both comparisons, then the margin,
  then the year to date. "Profit converts less": profit by month, then the month against budget. "Stock built up":
  inventory, then net debt, then working capital as a share of revenue. Steps move between the visuals and highlight
  within them.

A second visual must add a measure, a time span or a breakdown. The same rows against another comparison (against
budget, then against last year) is not an angle: show both comparisons in one chart (a `compare` tick, a `marker`, two
series) or with the switch. Across the story, months (bars and lines) belong in most chapters that argue a movement,
a bridge where a result moved against plan, ratios where the company profile names them as KPIs.

- **One frame.** Hold the story to one frame (the month, with budget and last year on the switch). A chapter on
  another basis (year to date, a balance against December) says so in its eyebrow.
- **The answer, the bridge, then the drivers.** The bridge comes before the chapters that explain its legs and stays
  at total level (revenue, cost of sales, overheads); the chapters after it add what a bridge cannot: rates, shares of
  revenue, reasons. Two chapters never draw the same legs.
- **Chart budget.** No visual type more than four times in a story; a three-column comparison only where the three-way
  contrast is the point; the hero's chart is never a chapter's main chart; six or more periods read as a line or bars.

## 4 · Draft without AI

```
story_create(storyId, pov: {year, period}, draft: {meta: {title, company, context, currency}, basedOn?: "<last period's documentId>", chapters: [{id, title, datasets}]})
```

finstory reads every dataset at once and saves a complete first draft: charts, KPI tiles, the budget/last-year switch
where the data has both, steps from the movements that stand out, and empty text. The reply gives:
- `draft.slots`: each empty text (title, lead, step bodies) and the figure names it may use;
- `draft.left_out`: datasets or chapters with no figures, and why. Tell the user what was left out;
- `evidence`: per chapter, the facts to weigh (section 5).

The draft draws one plain chart per dataset: it is scaffolding. Its sources, figures and evidence are what you keep;
the chapter design (section 3) is yours.

`pov` records the period the story covers; `basedOn` links it to last period's story, which is what makes "still
true?" work next.

## 5 · Ask before you guess

The evidence is finstory's list of facts, with no threshold applied. You judge what needs an explanation:

- **movement** with `timesUsual`: how many times its line's usual monthly move this movement is. A large `timesUsual`
  on a stable line is news even when the amount is small; a big amount on a line that always swings may be nothing.
  Without `timesUsual`, judge by size against the chapter's totals and by what the user cares about.
- **gap**: what a bridge's listed legs leave out. Ask when it is large next to the legs.
- **catchAll**: a balance on Uncategorized, Ask My Accountant or Suspense. Always worth one question: what it is, and
  whether it should be reclassified.
- **warning**: the data's own warnings (a line with actuals but no plan, a balance on the wrong side, a month that
  holds most of the year).
- **incompleteMonth**: the latest month at under half its usual level. Ask whether the month is closed.
- **stillTrue**: last period's answer, whose figure has since changed direction. Quote it and ask "still true?".
- Skip a movement marked `answered`.

Ask at most three questions per chapter, fewer when nothing stands out. Ask them together, chapter by chapter, each
naming the figure it is about in words ("Revenue came in well above budget, about three times its usual monthly
swing: what drove it?"). The user may answer some, skip some, or say "I don't know".

## 6 · Record the answers

All of them in one call, as soon as the user replies:
```
story_update(documentId, scroll: {answers: [{chapter, q, a, fig?}, ...]})
```
- `q` your question, `a` the user's answer in their own words (shorten, don't reword), `fig` the figure it explains:
  a figure the chapter already has (a drafted name); leave it out otherwise.
- finstory stamps who (the signed-in user, recorded via Claude) and when; the reply gives each answer's `id`.
- The answer text keeps the user's numbers word for word, and a step that cites the answer in its basis may write
  them out ("the fee rose from 3% to 5%"). Everywhere else a number is a placeholder.
- A correction replaces the earlier answer: `answer: {id, chapter, q, a}`.

## 7 · Write chapter by chapter

Replace each drafted chapter with its words:
```
story_update(documentId, scroll: {chapter: {...the drafted chapter from story_get, with title, lead and steps written}})
```
- Start from `story_get(documentId)`'s outline: keep its `sources` and `figures`, then give the chapter the angles
  its design calls for (section 3): turn each drafted chart into the type its angle needs (references/visuals.md),
  add a source for a missing angle, and drop a drafted chart that repeats another. Every step changes the picture.
- **Every step rests on something**, and says which:
  - **data**: its text uses the chapter's figures; nothing else to add;
  - **an answer**: `"basis": {"answers": ["a1"]}` when the step says what the user told you;
  - **an assumption**: `"basis": {"assumption": "Rebates are assumed to land in June, as last year."}` when the step
    is your own reading. Say it as a reading in the text too ("most likely…").
  A step can rest on data and an answer at once. A basis citing an answer the chapter does not have is refused.
- Never state a cause the data does not show without an answer or an assumption behind it.
- Carry the user's figures into the steps that cite them ("about $2M of actions", "terms cut from 60 to 45 days"):
  they are what a board remembers. Never name the process in reader text: no "management's answer", "the Q&A",
  "as the user said".
- In a chapter with the switch, every title, lead, KPI and step reads right under both options: a budget figure only
  in the budget text, a last-year figure only in the last-year text.
- Then the hero (`{hero}`) and the appendix, with `notes` listing the data gaps the story could not close.

Readers who switch Basis on see every step marked Data, Your answer or Assumption, with the source and period, the
answer word for word with who and when, or the assumption.

## 8 · Writing rules that matter most

- Headlines say the finding in two short sentences split by `|`, each under about 26 characters; the second shows in
  green. The words come from the figures: "Revenue {{revVar|updown}} {{revPct|pct abs}} on budget." reads "up" or
  "down" by itself.
- Name every comparison by what it is ("vs budget", "on last May"). A column labelled "LY" may hold something else:
  check one figure before trusting a label.
- A headline has a verb and a cause ("Freight, not volume, cost the margin"), not a restatement of the variance.
- A verb that claims a movement ("fell", "turned negative", "halved", "lengthening") needs a figure that clears
  materiality: at least 5%, or the board's own threshold. A comparison needs both sides in the data; without one,
  drop the claim.
- The same name with different values in two views (a channel and a product line with one name) gets one line
  saying why they differ.
- Steps: `when` 1–3 words, `title` the finding with its figure, `body` one or two sentences; about 100 words per
  chapter in all.
- Text may name years, period names (Q3, FY26, H1, P09), dates (30 Sep), chapter numbers, the story's own names, its
  sources' names, and any row or column label of the chapter's own sources ("4000 Sales"). Every other number is a
  placeholder.
- KPI subs at most 22 characters; short forms (`short`, `categoryShort`) for labels that can be cut on a phone.

## 9 · Review before you reply

Read the story back (`story_get`) and check every chapter; fix what fails, then reply:
- each step's chart shows the figure its title names;
- no visual type more than four times; no chapter repeats the hero's chart or another chapter's legs;
- every movement verb has a material figure behind it, and every comparison both sides;
- switched chapters hold nothing from the other comparison;
- the numbers of cited answers are in the steps, and no process words are;
- tones agree: a KPI tile and its bar carry the same colour, and a stock build is a use of cash.

## 10 · Reply

Two to four lines: what the story covers, what the user's answers added, what was left out or assumed, and the
story_url. The story is private to the user until a power user or admin publishes it in the app.
