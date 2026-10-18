---
name: finstory-scroll-story
description: Builds an interactive scroll story inside finstory (pinned chapters, step-by-step charts, KPI tiles, bridges, a budget/last-year switch, a Basis switch that shows what each step rests on, an appendix, dark mode and a phone layout) through the finstory connector. Claude agrees an outline from the user's request, the data, the company's memory and last period's story; finstory drafts it without AI and lists the facts that stand out; Claude asks the user about them, records the answers, and writes the words chapter by chapter, marking every claim as data, an answer or an assumption. finstory fills in and sources every number and refuses typed ones. Use whenever the user wants a finstory scroll story, a monthly or board story, an interactive, web or scrolly version of a finstory story, board pack or month-end narrative, "the 3STEP format", or a story page with a source on every number, even if they only paste a finstory story link or name a story.
---

# finstory scroll story

You build a scroll story straight into the user's finstory workspace. Each chapter is one argument: a pinned panel
with a chart on the right, steps down the left, KPI tiles above the chart. finstory draws it with its own engine and
keeps it private until a power user or admin publishes it.

**The one rule: you never write a number.** You write words and references; finstory reads every reference as the
saving user, fills it in and stores a receipt per number. A digit typed into text or chart data is refused, naming the
chapter and field. The exception is the user's own numbers: kept in their answers, and written out in the steps that
cite them.

**Trust and memory come first.** For every step a CFO can tell whether it comes from the data, from the user, or from
your own reading, and what the user tells you this month is kept with the story for next month.

## Work lean

Each tool result stays in the conversation for every later call, so read what you need once:
- **Tools.** The finstory tools you need are `company_get_context`, `report_list`, `report_preview_values`,
  `analysis_query`, `story_list`, `story_get`, `story_create` and `story_update`. Load them in one tool search where
  the host searches for tools (for example "finstory saved stories, reports, context and story create, update").
- **Context once.** Call `company_get_context(job: "build_story")` once. Its `tool_notes` are the server's own
  statement of the format (they win over this skill), and it also lists the accounts, scenarios and dimensions for
  `analysis_query`, and `company_profile` (the company's own background). Do not call it again with another job.
- **References when needed.** Read each file once, at the step that needs it, never all at once:

| file | read it when |
|---|---|
| `references/design.md` | planning the chapters (angles, chapter kinds, chart budget) |
| `references/visuals.md` | choosing a chart for a data shape |
| `references/reference-format.md` | before the first save: sources, figures, placeholders, answers, basis |
| `references/writing.md` | before writing: section 0 holds the rules that matter most |
| `references/spec.md` | looking up one field (read that section only) |
| `examples/chapter.json` | before the first chapter |

Everything about the story lives in finstory. Keep no copy of its data, answers or outline in artifacts, files or
notes of your own.

## 1 · Gather what the outline needs

In one round of calls where you can:
1. **The request**: the period, the audience (board, management team, owner), anything the user wants covered.
2. **The data**: `report_list` for the story-ready reports, then `report_preview_values` with `dataset: true` for the
   ones that matter, all in one message. Note what is *not* there: no budget, no classes or locations, no balance
   sheet.
3. **Memory**: `company_profile` (what the company sells, its segments and seasonality, KPI definitions, recurring
   drivers, the words the board uses).
4. **Last period's story**: `story_list` shows each scroll story's period. `story_get` on the latest earlier one, once,
   returns its chapters, datasets and `answers` (what the user explained last time). Offer to carry those answers
   over rather than asking again.

## 2 · Propose the outline

Per chapter an `id`, a working `title` (words only), the finding it argues and its `datasets`, one per angle
(references/design.md). Start from last period's chapters when there are any.

- **Chapters follow findings, not reports**: what moved, what explains it, what it does to the year and to cash. One
  report can feed several chapters, one chapter several reports; a slice (`analysis_query`) fills an angle no report
  shows, and a 12-month `trend` slice of a chapter's main line lets finstory say how unusual the month is.
- **Use the whole palette**: open on a statement chapter when the headline is the message, a stacked mix when shares of
  a total are the point, cards for a short list of actions, a timeline for a plan. Bridges, mix, conversion, cost,
  working-capital and forecast reports carry angles a P&L alone does not.
- **A finstory story** the user names (`story_get`) is a ready outline: each content page becomes a chapter.
- **Monthly or board story**: the headline month; revenue against budget and last year by the dimension the company
  manages by; gross margin; costs; the profit bridge; cash; the outlook; an appendix. **Small business without a
  budget** (typically QuickBooks): revenue against last year by customer and product, gross margin and top cost movers,
  balance sheet and cash, and a data-quality chapter for what the evidence flags; never invent a budget.
- Five chapters for a flash update, twelve to fifteen for a full board pack; a chapter without data is left out.

Show the outline as a short numbered list (title, finding in a line, its angles) and adjust it once. Don't read
figures out at this stage.

## 3 · Draft without AI

```
story_create(storyId, pov: {year, period}, draft: {meta: {title, company, context, currency}, basedOn?: "<last period's documentId>", chapters: [{id, title, datasets}]})
```
finstory reads every dataset at once and saves a first draft: charts, KPI tiles, the switch where the data has both,
steps from the movements that stand out, and empty text. The reply gives `draft.slots` (each empty text and the
figure names it may use), `draft.left_out` (tell the user) and `evidence` per chapter. The draft is scaffolding: keep
its sources, figures and evidence; the chapter design is yours. `basedOn` is what makes "still true?" work next period.

Before the first save, tell the user once, in one line: finstory asks permission for each save, and choosing "Always
allow" for the finstory Story tools lets the story finish without a prompt per chapter.

## 4 · Ask before you guess

The evidence is finstory's list of facts, with no threshold (reference-format.md section 10). You judge:
- **movement** with `timesUsual`: a large multiple on a stable line is news even when small; a big amount on a line
  that always swings may be nothing. Skip a movement marked `answered`.
- **gap** (what a bridge's legs leave out), **catchAll** (Uncategorized, Ask My Accountant, Suspense: always one
  question), **warning**, **incompleteMonth** (ask whether the month is closed), **stillTrue** (quote last period's
  answer and ask "still true?").

At most three questions per chapter, asked together, chapter by chapter, each naming its figure in words. The user may
answer some, skip some, or say "I don't know".

## 5 · Record the answers

All of them in one call, as soon as the user replies:
```
story_update(documentId, scroll: {answers: [{chapter, q, a, fig?}, ...]})
```
`a` is the user's answer in their own words (shorten, don't reword); `fig` a figure the chapter already has. File each
answer under the chapter whose steps will cite it: a step may cite only its own chapter's answers. finstory stamps who
and when and returns each `id`; `answer: {id, ...}` replaces one.

## 6 · Write the chapters, several per save

```
story_update(documentId, scroll: {chapters: [{...}, {...}, {...}]})
```
- Start from the drafted chapters (`story_create`'s reply, or `story_get` once): keep their `sources` and `figures`,
  give each the angles its design calls for, turn drafted charts into the type each angle needs, drop one that repeats
  another. Every step changes the picture.
- **Save three or four chapters per call**, and the hero, appendix and `notes` (the data gaps the story could not
  close) together in the last one. A refusal saves nothing and names the chapter and field: fix that chapter and save
  the same list again.
- **Write both switch positions before you save.** In a chapter with the switch, every title, lead, KPI and step reads
  right under both options; check each claim against both comparisons first, so a chapter is written once.
- **Every step rests on something**: data (its text uses the chapter's figures); an answer (`"basis": {"answers":
  ["a1"]}`); or an assumption (`"basis": {"assumption": "Rebates land in June, as last year."}`, said as a reading in
  the text too: "most likely…"). Never state a cause the data does not show without one of these.
- Carry the user's figures into the steps that cite them; they are what a board remembers. Never name the process in
  reader text: no "management's answer", "the Q&A", "as the user said".

## 7 · Review before you reply

Use the save replies rather than reading the story back: `charts` (no type above four), `repeatsHero` (absent),
`stepsWithoutBasis` (give each listed step its basis, or confirm it rests on data). Then check that each step's chart
shows the figure its title names, no chapter repeats another's legs, every movement verb has a material figure and
every comparison both sides, the cited answers' numbers are in the steps, and tones agree (a stock build is a use of
cash). Fix what fails in one more save.

## 8 · Reply

Two to four lines: what the story covers, what the user's answers added, what was left out or assumed, and the
story_url. The story is private to the user until a power user or admin publishes it in the app.
