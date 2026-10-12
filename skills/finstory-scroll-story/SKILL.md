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
names the chapter and field. The one place a typed number is kept is an answer the user gave (section 5).

**Trust and memory come first.** A CFO must be able to tell, for every step, whether it comes from the data, from
something the user told you, or from your own reading. And what the user tells you this month is kept with the story,
so next month starts from it.

| file | what it is | read it |
|---|---|---|
| `references/reference-format.md` | sources, figures, placeholders, calculations, series, bridges, answers, basis, evidence, the draft | before the first save |
| `references/spec.md` | every field of a chapter, visual and step (the shape finstory draws) | when you need a field |
| `references/visuals.md` | which visual for which data shape | before rewriting a drafted chart |
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

An outline is the chapter list before any words: per chapter an `id`, a working `title` (words only, no figures) and
its `datasets` (sources, reference-format.md section 1). It is bespoke: start from last period's chapters when there
are any, then adjust to this month's request, data and memory.

Guidelines, not a template:
- **Monthly management or board story**: the headline month; revenue against budget and last year, by the dimension
  the company manages by; gross margin; costs; EBITDA or profit bridge; cash; a detail or appendix table.
- **Small business, no budget** (typically QuickBooks): the headline month; revenue against last year, by customer and
  product where the books hold them; gross margin and the top cost movers; balance sheet, cash and working capital;
  class or location mix only if tracked; a data-quality chapter for whatever the evidence flags. Compare with last
  year and the prior month; never invent a budget.
- A monthly trend slice of a chapter's main line (`analysis_query` `trend`, 12 months) lets finstory say how unusual
  this month's movement is for that line. Add one where a chapter's finding is a movement.
- Six to nine chapters. A chapter without data is left out, never shown empty.

Show the outline to the user as a short numbered list (chapter titles and what each shows) and adjust it once. Don't
read figures out to them at this stage.

## 3 · Draft without AI

```
story_create(storyId, pov: {year, period}, draft: {meta: {title, company, context, currency}, basedOn?: "<last period's documentId>", chapters: [{id, title, datasets}]})
```

finstory reads every dataset at once and saves a complete first draft: charts, KPI tiles, the budget/last-year switch
where the data has both, steps from the movements that stand out, and empty text. The reply gives:
- `draft.slots`: each empty text (title, lead, step bodies) and the figure names it may use;
- `draft.left_out`: datasets or chapters with no figures, and why. Tell the user what was left out;
- `evidence`: per chapter, the facts to weigh (section 4).

`pov` records the period the story covers; `basedOn` links it to last period's story, which is what makes "still
true?" work next.

## 4 · Ask before you guess

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

## 5 · Record the answers

For each answer:
```
story_update(documentId, scroll: {answer: {chapter, q, a, fig?}})
```
- `q` your question, `a` the user's answer in their own words (shorten, don't reword), `fig` the figure it explains.
- finstory stamps who (the signed-in user, recorded via Claude) and when; the reply gives the answer's `id`.
- The answer text keeps the user's numbers word for word. They stay in the answer: the narrative may quote or
  paraphrase the answer, but a number the user said appears only inside it, never as a typed figure in a step.
- A correction replaces the earlier answer: pass its `id`.

## 6 · Write chapter by chapter

Replace each drafted chapter with its words:
```
story_update(documentId, scroll: {chapter: {...the drafted chapter from story_get, with title, lead and steps written}})
```
- Start from `story_get(documentId)`'s outline: keep its `sources`, `figures` and visuals, rewrite the words, and
  change a visual only when references/visuals.md calls for another one.
- **Every step rests on something**, and says which:
  - **data**: its text uses the chapter's figures; nothing else to add;
  - **an answer**: `"basis": {"answers": ["a1"]}` when the step says what the user told you;
  - **an assumption**: `"basis": {"assumption": "Rebates are assumed to land in June, as last year."}` when the step
    is your own reading. Say it as a reading in the text too ("most likely…").
  A step can rest on data and an answer at once. A basis citing an answer the chapter does not have is refused.
- Never state a cause the data does not show without an answer or an assumption behind it.
- Then the hero (`{hero}`) and, if useful, the appendix.

Readers who switch Basis on see every step marked Data, Your answer or Assumption, with the source and period, the
answer word for word with who and when, or the assumption.

## 7 · Writing rules that matter most

- Headlines say the finding in two short sentences split by `|`, each under about 26 characters; the second shows in
  green. The words come from the figures: "Revenue {{revVar|updown}} {{revPct|pct abs}} on budget." reads "up" or
  "down" by itself.
- Name every comparison by what it is ("vs budget", "on last May"). A column labelled "LY" may hold something else:
  check one figure before trusting a label.
- Steps: `when` 1–3 words, `title` the finding with its figure, `body` one or two sentences.
- Text may name years, period names (Q3, FY26, H1, P09), dates (30 Sep), chapter numbers, the story's own names, its
  sources' names, and any row or column label of the chapter's own sources ("4000 Sales"). Every other number is a
  placeholder.
- KPI subs at most 22 characters; short forms (`short`, `categoryShort`) for labels that can be cut on a phone.

## 8 · Reply

Two to four lines: what the story covers, what the user's answers added, what was left out or assumed, and the
story_url. The story is private to the user until a power user or admin publishes it in the app.
