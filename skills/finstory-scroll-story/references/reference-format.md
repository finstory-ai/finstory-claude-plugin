# The reference format

A scroll story is saved as blocks: the `hero`, each chapter, the `appendix`. Each block lists where its numbers come
from (`sources`), names the numbers it uses (`figures`), and then uses them in its text and charts. finstory reads
every source as the saving user, fills every figure in, and keeps a receipt for each one. `meta`, `lens` and `notes`
hold words only.

Contents: 1 Sources · 2 Figures · 3 Text placeholders · 4 Chart values and series · 5 Tables · 6 Bridges ·
7 What is refused · 8 Saving and changing · 9 Answers and basis · 10 Evidence · 11 The draft

## 1 · Sources

```jsonc
"sources": {
  "pl":     {"report": "<report_id>", "pov": {"year": "2026", "period": "Sep"}},          // a whole report
  "kpis":   {"report": "<report_id>", "component": "<component_id>"},                     // one component of it
  "stores": {"slice": {"template": "cross_tab", "params": {…}, "pov": {…}}}                // a slice of any data
}
```
- A report source reads the report as `report_preview_values(report_id, dataset: true, pov)` shows it: one table per
  component, columns by position, rows by label. `pov` goes over the report's own slice, as in that tool.
- A slice source is `cells.slice` from `analysis_query(…, raw_cells: true)` (template, params, pov; add `model` when
  the workspace has several). Pass it back as it came, so the question is asked again exactly.
- Names (`pl`, `stores`) are yours; they only need to be unique within the block.
- Readers see each source once under the chapter's chart, as "Source: <report name> · <period>" (a slice reads
  "Analysis of <accounts>"). A `source` you write on the panel or a chart replaces it. finstory keeps the full
  detail (report id and version, slice, read time) with the story.

## 2 · Figures

```jsonc
"figures": {
  "rev":    {"$ref": "pl", "row": "Revenue", "col": "Actual"},
  "revBud": {"$ref": "pl", "row": "Revenue", "col": 1},
  "revYtd": {"$ref": "pl", "row": "Revenue", "col": 4, "table": "<component_id>"},
  "revVar": {"$calc": "diff",  "of": ["rev", "revBud"]},
  "revPct": {"$calc": "pct",   "of": ["rev", "revBud"]},
  "share":  {"$calc": "share", "of": [{"$ref": "stores", "row": "Example Store A", "col": 0}, "storeTotal"]},
  "storeTotal": {"$calc": "sum", "of": [{"$ref": "stores", "row": 0, "col": 0}, {"$ref": "stores", "row": 1, "col": 0}]}
}
```
- `$ref`: a cell of a source. `row` and `col` by label (exact, then ignoring case) or by position from 0. A label that
  appears twice is refused: give its position. `table` picks a component (its id or position); without it, the first
  table that has the row.
- `$calc`, the only five calculations:
  - `diff` a − b
  - `pct` (a − b) / |b|; empty when b is 0
  - `share` a / b
  - `ratio` a / b
  - `sum` of one or more
- An input is a figure name, a `$ref` or another `$calc`. Anything richer belongs in a report formula column.
- Each figure's receipt records its source, row, column and value, or its calculation with the receipts of its inputs.

## 3 · Text placeholders

Any text field can hold `{{name|format}}`:

| format | reads | use |
|---|---|---|
| `money` (the default) | $12.7M, $354K, $950 | money in text and KPI tiles |
| `k` / `m` | 12,705 / 12.7 | table cells in $000 or $M |
| `num` | 1,250 | counts |
| `pct` | 39.4% | a ratio, a % change, a share |
| `pts` | 1.4 pts | a change in a ratio |
| `x` | 2.5x | a multiple |
| `updown`, `higherlower`, `overunder`, `betterworse`, `tone` | up/down/flat, higher/lower/unchanged, over/under/on, better/worse/in line, good/bad/flat | words from the sign |

- `.0` to `.3` set the decimals (`pct.2`), `+` signs a rise (`money +` → +$354K), `abs` drops the sign
  (`{{revVar|money abs}}` → $354K when you wrote the direction in words).
- A trailing `-` on a word format reads a rise as worse, for costs: `{{opexVar|betterworse-}}`, `{{opexVar|tone-}}`.
- Negatives read the way the workspace prints them: −$120K, or ($120K) where it uses parentheses.
- KPI tiles: `"value": "{{rev|money}}", "delta": "{{revVar|money +}}", "tone": "{{revVar|tone}}", "sub": "vs {{revBud|money}} budget"`.

## 4 · Chart values and series

- A chart's `value`, `compare` and each item of `values` is a figure: its name (`"value": "rev"`), a `$ref` or a
  `$calc`. finstory writes it in the chart's `unit`: `k` divides by 1,000, `m` by a million, `b` by a billion; `pct`
  stays a fraction.
- A whole series in one go:
  - `{"$series": "pl", "row": "Revenue", "cols": [0, 1, 2]}` — one row across columns (all columns when `cols` is
    left out)
  - `{"$series": "stores", "col": "Actual", "rows": ["Example Store A", "Example Store B"]}` — one column down rows
    (all rows when `rows` is left out)
- Categories and labels are words you write; they must match the series length (finstory's checker says so when they
  do not).

## 5 · Tables

A table cell (`cells`) is text: a placeholder (`"{{rev|k}}"`) or a figure with a format
(`{"$ref": "pl", "row": "Revenue", "col": 1, "fmt": "k"}`). A whole row of cells can be one series with a format:
`"cells": {"$series": "pl", "row": "Revenue", "cols": [0, 1, 2], "fmt": "k"}`.

## 6 · Bridges

A waterfall's `start`, `legs` and `end` values are figures. When the legs do not add up to the end, finstory adds one
more leg itself, "Other", with its own receipt (the figures are unrounded, so a gap is always what the listed legs
leave out). So list the legs that matter (seven at most) and let finstory show the rest.

## 7 · What is refused

- A digit in text, unless it is part of a year (2026, 2025/26), a period name (Q3, FY26, H1, P09, W40), a date
  (30 Sep, Sep 30), a chapter number ("Chapter 2"), a unit ("$000"), a name (a word that starts with a letter, such
  as B2C, or a word of `meta.company`, `meta.title` or `meta.context`), a source's name, or a row or column label
  of one of the block's sources that has a letter in it ("4000 Sales"); a label made only of digits must be the whole
  text. "Top 10 stores" and "12M" are refused: write "the ten largest stores" or name them, and use a placeholder for
  the amount.
- A number typed into a chart's `value`, `values` or `compare`, or into a table cell.
- A reference that does not resolve: an unknown source, row, column or figure, an ambiguous label, a calculation
  with the wrong number of inputs.

The error lists every field at fault, block by block, and nothing is saved; fix them and save the block again.
Axis settings (`yMin`, `yMax`, `axisFrom`) and indexes (`visual`, `kpi`, `reveal`, `delta.from`) stay plain numbers.

## 8 · Saving and changing

- `story_create(storyId, pov?: {year, period}, scroll: {meta, lens?, hero?, chapters: [...], appendix?, notes?, basedOn?})`
  — every chapter needs a unique `id`. `pov` is the period the story covers (story_list and story_get show it);
  `basedOn` is the documentId of the earlier story it follows.
- `story_update(documentId, scroll: {chapter})` replaces the chapter with that id or adds it at the end;
  `{removeChapter: "<id>"}` drops one; `{hero}`, `{appendix}`, `{meta}`, `{lens}`, `{notes}` replace theirs. Only the
  changed block is read again; a new `meta` re-reads every block, because the currency may have changed.
- Both return the chapter ids, how many figures were filled, from how many sources and when, never the figures.
- `story_get(documentId)` returns `outline`: the blocks as you wrote them, with their references.
- Once saved, the story stands alone: changing or deleting a report later changes nothing in it.

## 9 · Answers and basis

```jsonc
// record what the user said; finstory adds the id, who (recorded via Claude) and when
"scroll": {"answer": {"chapter": "revenue", "q": "What drove revenue above budget?", "a": "Two wholesale orders landed early.", "fig": "revVar"}}
// a step that rests on it, and one that rests on your own reading
"steps": [
  {"title": "Revenue {{revVar|money +}} vs budget", "body": "Two wholesale orders landed early.", "basis": {"answers": ["a1"]}},
  {"title": "June should give some back", "body": "Most likely a timing effect.", "basis": {"assumption": "The early orders were June's."}}
]
```
- Only `scroll.answer` writes answers; a chapter that carries `answers` itself is refused. `id` replaces an earlier
  answer, `fig` names the chapter figure it explains (that is what makes "still true?" work next period).
- Answer text is kept word for word, numbers included. An assumption is checked like any other text: no typed numbers.
- `basis.answers` must name answers of the same chapter. A step whose text uses figures already rests on data.
- `story_get` returns `outline.answers` and `outline.basedOn`; the drawn chapter carries its answers for the Basis
  switch.

## 10 · Evidence

Every save returns `evidence` for the chapters it read; `story_get` keeps every chapter's in `outline.evidence`.
finstory applies no threshold:

| kind | what it says |
|---|---|
| `movement` | a named difference: `fig`, `about` ("Revenue: Actual vs Budget"), `value`; `usualMonthlyMove` and `timesUsual` where the chapter's sources hold a monthly series of that line; `answered` when an answer names it |
| `gap` | what a bridge's listed legs leave out (the Other leg) |
| `warning` | a slice's data warning: `code`, `member`, `detail` |
| `catchAll` | a balance on an Uncategorized, Ask My Accountant or Suspense row |
| `incompleteMonth` | a line's latest month under half its usual level |
| `stillTrue` | an answer in the `basedOn` story whose figure changed direction: `was`, `now`, the answer |

## 11 · The draft

```jsonc
"draft": {"meta": {...}, "basedOn": "<documentId>", "chapters": [
  {"id": "revenue", "title": "Revenue", "datasets": {"pl": {"report": "<report_id>"}, "trend": {"slice": {...}}}}
]}
```
- Datasets are sources (section 1). finstory reads them all at once and drafts per dataset: a monthly series as a
  `line`; actual against budget or last year as `hbars` (`columns` for a single line); one column as ranked
  `hbars`; up to three KPI tiles; the lens only when the data holds both budget and last year; up to four steps from
  the movements largest against their usual monthly move; empty lead and step bodies.
- Figures are named `d<dataset>r<row>_<role>`: `_act`, `_bud`, `_ly`, and the differences `_dbud`, `_dly`.
- The reply adds `draft.slots` (each empty text and the figures it may use) and `draft.left_out`.
