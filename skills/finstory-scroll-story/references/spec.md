# Story spec reference

> **How this applies inside finstory.** This file lists every field finstory's story engine draws. You write the same
> fields through the connector, but every number is a reference (reference-format.md): the numbers shown below are
> what finstory fills in. finstory draws bars, hbars, columns, waterfall, line, table and cards; stacked, meter,
> heatmap, scatter, timeline and html are refused, as are line `bands`/`refs`, step `zoom`/`upto`, the theme
> button and `meta.storyUrl`'s link. Sections 9's `storylib`/`storykit` checks are replaced by finstory's own
> checks on save and in the app.

The page is one JSON spec (`story.json`) rendered by `assets/engine.js`. Builders write it with
`scripts/storylib.py`; this file lists every field the engine reads, so you can also write or patch JSON by hand.

Contents: 1 Top level · 2 Lens · 3 Hero · 4 Chapters · 5 Steps and focus · 6 Visual types · 7 Units, roles, formats ·
8 Appendix and notes · 9 What the engine and storylib check

## 1 · Top level

```jsonc
{
  "v": 1,
  "meta": {
    "title": "B2C May Board Story",        // the page name, 2–4 words (browser tab)
    "company": "Born2Cycle Group",          // top bar
    "context": "May 2026 Board Review",     // top bar, after the company
    "description": "one line",              // page description
    "storyUrl": "https://app.finstory.ai/story/<id>",   // "Open in finstory" button (https only)
    "currency": "$",                        // symbol used by axes, tooltips and money units
    "source": "Built from the finstory story “…” · figures as of 8 Oct 2026"   // footer
  },
  "lens": { … },        // optional compare switch, section 2
  "hero": { … },        // section 3
  "chapters": [ … ],    // section 4, one per story argument
  "appendix": { … },    // optional, section 8
  "notes": { … }        // optional, section 8
}
```

## 2 · Lens (the compare switch)

```json
"lens": {"label": "Compare", "default": "bud",
         "options": [{"id": "bud", "label": "vs Budget", "short": "Budget"},
                     {"id": "ly", "label": "vs Last year", "short": "Last yr"}]}
```
`storylib.COMPARE` is exactly this. Any value anywhere in the spec may then be a lens map,
`{"$lens": {"bud": <value>, "ly": <value>}}`: text, numbers, KPI tones, a whole visual, a step focus field, a table
row's cells, an appendix table. `LZ(bud, ly)` writes one (and collapses to a plain value when both are equal);
`lens_map(rep=…, adj=…)` does the same for any other option ids. Every lens map must name every option.
A second kind of switch, a basis ("Reported" / "Adjusted", "Consolidated" / "Unconsolidated"), is the same mechanism
with other ids. A story has at most one lens.

## 3 · Hero

```jsonc
"hero": {
  "eyebrow": "May 2026 Board Review · Born2Cycle",
  "title": "Sales beat plan. | Profit beat it by more.",   // "|" splits it; the second part shows in green
  "lead": "One or two sentences.",
  "strong": "One sentence on the watch-item (shown bold).",
  "meta": ["13 board pages · 11 chapters", "Figures in $000 unless noted"],   // up to 4
  "card": {                                       // optional right-hand panel
    "title": "Revenue by month · $M", "chip": "Jan–May",
    "stat": {"label": "EBITDA, May", "value": "$1.32M", "delta": "+$354K", "tone": "good", "sub": "vs $971K budget"},
    "visual": { /* any chart visual, drawn small: bars or line work best */ },
    "trio": [{"label": "Revenue", "value": "$12.7M", "sub": "+5.0% vs budget"}]   // up to 3
  }
}
```

## 4 · Chapters

```jsonc
{
  "id": "ebitda",                       // unique slug
  "rail": "EBITDA",                     // the side-rail label, one or two words
  "section": "Profit",                  // optional group name, shown before the eyebrow (hidden on phones)
  "eyebrow": "May 2026 · EBITDA bridge",  // period · topic; the engine adds the chapter number
  "title": "EBITDA beat plan by $354K. | All of it from gross margin.",
  "lead": "optional one sentence under the title (hidden on phones and short screens)",
  "panel": {"title": "May EBITDA · budget to actual, $000", "chip": "vs budget", "chipTone": "info",
            "source": "Report “Conversion GP into EBITDA”"},
  "kpis": [{"label": "EBITDA", "value": "$1.32M", "delta": "+$354K", "tone": "good", "sub": "vs $971K budget"}],  // up to 4 shown per row
  "filter": {"label": "Highlight a channel", "keys": ["Online - Direct", "In-Store Sales"], "all": "All"},
  "visuals": [ /* one or more visuals, section 6 */ ],
  "steps": [ /* section 5 */ ],
  "tint": true                          // optional: force the tinted background (default alternates)
}
```
- `panel.title` is the default panel head; a visual's own `title` replaces it while that visual shows. Same for `source`.
- `kpis`: tiles above the chart. The arrow follows the sign of `delta` (▲ for "+", ▼ for "−"), the colour follows
  `tone` (`good`, `bad`, `flat`), so a cost that fell reads "▼ −6.2%" in green. A delta that is empty or `None` (also
  under one lens option) is hidden. Clicking a tile jumps to the first
  step that points at it. Keep `sub` to one comparison of at most 22 characters and `label` to 20: longer ones are
  cut on phones and in rows of four tiles (`validate` warns).
- `filter`: chips above the chart. A chip highlights its key in every visual of the chapter that has it (a category,
  series, row, leg, point label or group) and leaves the others alone. Keys must match the visual labels exactly.

## 5 · Steps and focus

```jsonc
{"when": "Revenue", "title": "Revenue +$605K vs budget", "body": "One or two sentences.",
 "visual": 1, "kpi": 0, "rows": ["Revenue"]}
```
- `when`: 1–3 words (green, uppercase). `title`: the number and the finding. `body`: one or two sentences.
- `visual`: index into the chapter's `visuals`; when left out, the previous step's visual stays.
- `kpi`: a tile index, or an array, to highlight.
- `basis`: what the step rests on beyond its figures, `{"answers": ["a1"]}` (the user's answers of this chapter) or
  `{"assumption": "…"}` (your reading). Readers who switch Basis on see each step marked Data, Your answer or
  Assumption (reference-format.md section 9). A chapter's `answers` are added by finstory, never written.
- Focus fields (on the step itself, or inside `"focus": {}`):

| field | works on | effect |
|---|---|---|
| `cats: ["May"]` | bars, line, columns, heatmap (columns) | highlight categories, dim the rest |
| `series: ["2026"]` | bars, stacked, scatter (groups) | highlight series |
| `rows: ["Revenue"]` | hbars, table, heatmap, waterfall (legs by `label` or `short`), columns, scatter (point labels) | highlight rows |
| `reveal: 2` / `"all"` | waterfall | legs shown: 0 = start only; `"all"` adds the end bar and the pill |
| `callout: {"cat": "Jul", "text": "Record $10.0M", "series": "2026"}` | bars, line | a label above one bar or point; an array for several |
| `upto: 5` | line | draw the lines up to category index 5 (a reveal over time) |
| `zoom: [6, 11]` | line | zoom the x-axis to category indexes 6–11 |
| `bands: true` | line | show the line's `bands` |
| `item: 1` | cards | highlight a card (or an array) |
| `phase: 2` | timeline | the active phase |

Each step should change what the chart shows: another visual, another highlight, another leg revealed.

## 6 · Visual types

Every visual takes `title` (panel head while it shows), `note` (small text under it; hidden on phones),
`source`, `dp` (decimals of its labels), `height: "short" | "tall"`, `legend: false`, and
`table: {"head": [...], "rows": [[...]], "note": "…"}` to replace what the panel's Data button shows (the engine
builds that table itself for every type except `html`). Labels that can be cut on a phone take a short form: `short`
on waterfall legs, start and end, hbars rows, columns items and scatter points; `categoryShort[]` on bars, line and
stacked. The short form is used where the full label would be cut, and then by every sibling that has one, desktop
included, so a short form must read well on its own.

| type | fields | storylib |
|---|---|---|
| `bars` | `unit`, `categories[]`, `series[{name, role, values[], forecastFrom, forecastLabel}]`, `marker{name, values[]}` (budget / last-year ticks on the focal series), `labels: "auto" \| "all" \| "focal" \| "none"`, `categoryShort[]`, `yMin`, `yMax`, `dp` | `bars(cats, [(name, values, role)], unit, marker=(name, values))` |
| `line` | `unit`, `categories[]`, `series[{name, role, values[] (null ends a line), dashedFrom, forecastLabel, area}]`, `labels: "auto" \| "all" \| "none"`, `bands[{from, to, label, fromCat, tone: "a" \| "b"}]`, `showBands`, `bandsLegend`, `refs[{value, label, tone: "good"}]`, `zeroBased` (false for ratios), `hideEndLabels`, `categoryShort[]` | `line(cats, series, unit, **kw)` |
| `hbars` | `unit`, `mode: "value" \| "variance"`, `rows[{label, short, value, compare, note, role}]`, `total{label, short, value, note}`, `sort: "desc" \| "asc" \| "abs"` (default: as given), `valueName`, `compareName`, `goodLabel`, `badLabel`, `tipNote` | `hbars([(label, value, note, compare)], unit, mode, total=(label, value), shorts={})` |
| `waterfall` | `unit`, `legUnit` (format of the legs and the pill when it differs, e.g. totals `pct`, legs `pts`), `start{label, value, short}`, `legs[{label, value, short, tone}]`, `end{label, value, short}`, `invert` (cost bridge: up is bad), `pill`, `pillTone`, `vsLabel`, `axisFrom`, `allowGap`, `layout: "auto" \| "vertical" \| "horizontal"` | `bridge((label, v), [(label, v, short)], (label, v), unit)` |
| `stacked` | `categories[]`, `categoryShort[]`, `series[{name, values[]}]` (max 5), `mode: "share" \| "abs"`, `totals[]` (text above columns), `unit`, `valueUnit` (share mode tooltips) | `stacked(cats, series, mode, unit)` |
| `columns` | `unit`, `items[{label, short, sub, value, role}]`, `delta{from, to, text}` (a bracket and pill between two items) | `columns([(label, value, role, sub)], unit, delta=(0, 2[, text]), shorts={})` |
| `meter` | `unit`, `domain[lo, hi]`, `value{label, value}`, `marks[{label, value, role}]`, `goal{label, value}`, `caption` | `meter((label, v), domain, marks, goal)` |
| `table` | `columns[]`, `rows[{label, cells[], level: "detail" \| "sub" \| "total" \| "ratio", invert}]`, `signCols[]` (green/red by sign; `invert` flips a row), `corner`, `note` | `table(cols, [pl_row(...)], sign_cols=[2, 3])` |
| `heatmap` | `unit`, `columns[]`, `rows[{label, values[], level, invert}]`, `mode: "diverging" \| "sequential"` (default: diverging when any value is negative), `invert`, `max` (caps the colour scale), `goodLabel`, `badLabel`, `corner`, `dp` | `heatmap(cols, [(label, values, invert)], unit, mode)` |
| `scatter` | `xUnit`, `yUnit`, `points[{label, short, x, y, size, group, role}]`, `xLabel`, `yLabel`, `xName`, `yName`, `sizeName`, `sizeUnit`, `refX`, `refY` (a number or `{value, label}`), `quadrants{tl, tr, bl, br}`, `labels: "auto" \| "all" \| "none"`, `xMin/xMax/yMin/yMax`, `xZero`, `yZero` | `scatter([(label, x, y, size, group)], x_unit, y_unit, x_label, y_label, shorts={})` |
| `cards` | `items[{k, title, value, body}]`, `cols` (max 3) | `cards([(k, title, body)])` |
| `timeline` | `phases[{label, title, body}]`, `marks[{at, text}]`, `warn`, `warnLabel`, `list[]` | `timeline([(label, title, body)])` |
| `html` | `html` (an HTML string, no scripts), `table` for the Data button | `html(markup, table=…)`, `spark_tiles(items)` |

Notes on behaviour:
- **bars**: one series is `focal`; two default to `context`, `focal` (last year grey, this year blue); three to
  `context`, `total`, `focal`. Value labels (`labels`): `"auto"` labels every bar when there is room and one series,
  otherwise the focal series' max, min (when negative) and last; `"all"` labels every bar of every series (the
  comparison series in grey); `"focal"` every bar of the focal series; `"none"`. Two measures in one bar chart is a
  smell: one chart per measure. `forecastFrom: 6` draws the focal bars from index 6 in light blue with a legend.
- **line**: end labels name each series when there are two or more. Value labels (`labels`): `"auto"` labels the max
  and the last point (every point for one short series), `"all"` every point of the focal series, `"none"`; labels
  that would collide are dropped, the latest point winning, and a `callout` replaces the label at its point.
  `dashedFrom` dashes the series from that index. `area: true` shades under one series. `zeroBased: false` lets the
  axis start near the data (ratios, margins).
- **hbars**: `variance` mode colours by sign (green better, red worse) and signs the labels; `note` prints after the
  value on desktop. `compare` draws a dark tick (a budget or prior-year mark), which makes a bullet chart. In value
  mode the `total` is printed under the rows, not drawn, so a total far larger than its rows is fine.
- **waterfall**: start + legs must equal end within 0.5% of the end (rounding). Positive legs are green, negative red;
  `invert: true` swaps that for cost bridges. The axis is cut automatically when every level sits far above zero
  (break marks on the total bars and an "Axis starts at" note); `axisFrom` forces a base. Negative starts and ends are
  fine. The pill shows end − start and, when start and end have the same sign, the % of the start, "vs <vsLabel or start
  label>"; `pill` replaces the text. Pills round money like `money()` ($1.3M, $59K).
  The automatic cut applies to money and counts; ratio bridges (`pct`, `pts`, `x`) start at zero unless `axisFrom`
  zooms in. Set `axisFrom: 0` whenever the size of the totals against each other is the finding.
  `legUnit` formats the legs (and the pill) differently from the totals: a margin walk has totals in `pct` and legs
  in `pts`. On a phone, a bridge with 7 or more columns, or whose labels would be cut, is drawn sideways (one row per
  step, labels on the left); `layout` forces either form. Give long legs a `short` form anyway: once any label has to
  be cut, every leg that has a short form uses it, so sibling legs stay consistent (the same holds for hbars rows and
  columns items).
- **stacked**: share mode normalises each column to 100% and labels segments with their share; abs mode stacks values.
- **columns**: roles default to `context` (first), `total` (middle), `focal` (last). The delta pill shows the
  difference and its % of the first item, or the difference in pts when `unit` is `pct`; `delta.text` replaces it. Bracket two neighbouring items: a pill that
  spans a taller column covers that column's value label (`validate` warns).
- **heatmap**: an HTML grid; cells show the value, coloured green/red by sign (diverging) or blue by size (sequential).
  `invert: true` on a cost row makes positive read as worse. It scrolls sideways inside the panel on phones when wide.
- **scatter**: points coloured by `group` (up to five, with a legend) or `role`. With `size`, points become bubbles
  (area ∝ size). Labels: all when there are 8 or fewer points (5 on phones), otherwise the extremes. Highlighted
  points (a step's `rows` or `series`, or a chip) are labelled first and always shown; other labels that would
  collide are dropped.
- **table**: columns whose cells are mostly words are left-aligned and wrap; numeric columns align right.
- **html**: put text in HTML (grid, flex), not in SVG (SVG text shrinks with the panel and is unreadable on phones);
  colour with `var(--focal)`, `var(--focal-soft)`, `var(--context)`, `var(--ink)`, `var(--ink-2)`, `var(--muted)`,
  `var(--green-mark)`, `var(--bad-mark)`, `var(--line)`, `var(--surface)` so dark mode works. The panel shows it at its
  natural height; keep it under about 380px on desktop.

## 7 · Units, roles, formats

**Units** (the unit the values are written in): `k` (thousands, axis "$000"), `m` (millions, "$M"), `b`, `money`
(raw currency, auto-compact: $12.7M), `pct` (fractions: 0.394 → 39.4%), `pts` (fraction differences: 0.014 → 1.4 pts),
`bps`, `x` (multiples), `days` (a cash cycle, DSO: axis "days"), `num` (plain). `dp` sets the decimals. Tooltips show money units as compact currency.

**Roles** (colour by meaning): `focal` (blue, this year), `soft` (light blue, forecast), `context` (grey, last year),
`total` (dark, budget or a total), `good` / `bad` (green / red: better / worse only), `ink`, `s1`–`s5` (categorical
segments, in that fixed order).

**Text conventions** (storylib formats all of these): a typographic minus (−) for negatives in text; table cells in
accounting style, `(237)` for negatives; `n.m.` for a % that isn't meaningful; money in text as $12.7M / $354K.

## 8 · Appendix and notes

```jsonc
"appendix": {
  "eyebrow": "Appendix · the detail", "title": "Five tables. | Every figure, in full.", "lead": "optional",
  "tables": [{"title": "P&L · May", "note": "$000. B/(W): better / (worse) than the comparison.",
              "columns": ["Actual", "Budget", "B/(W)", "B/(W) %"], "signCols": [2, 3], "barCol": 2, "corner": "",
              "rows": [{"label": "Net revenue", "cells": ["12,705", "12,100", "605", "5.0%"], "level": "total"}]}]
},
"notes": {
  "title": "About this story",
  "items": [{"k": "Source", "title": "The finstory story and its reports", "body": "…"}],
  "gaps": {"title": "Data notes", "items": ["Every data quirk found, one line each."]}
}
```
`barCol` draws a small diverging bar from that column's figure. Tables are tabs; a lens map on a table (or on its
title, columns or rows) follows the switch.

## 9 · What the engine and storylib check

`storylib.validate` (run by `write()`) and the engine check the same things, for every lens option:
- every lens map names every option, and the story defines a lens when lens maps are used
- chapters exist, ids are unique, every chapter has visuals, every step points at a visual and KPI that exist
- known visual types; series have one value per category; values are numbers or null; markers match categories
- waterfalls close (0.5%); hbars values are numbers; table and appendix rows have one cell per column
- heatmap rows have one value per column; scatter points have numeric x and y; html has no scripts
- warnings: step titles over 60 characters, bodies over 260, chapter titles over 80, waterfalls over 8 legs,
  stacked over 6 series, scatter over 40 points, heatmap over 13 columns, KPI subs over 22 characters (labels over
  20), a columns delta that spans a column

`storykit check` then renders the page and reports overlapping or clipped chart labels, sideways scrolling,
script errors, chapters too tall to pin on desktop, charts that fail to draw when replayed with animations, and a
first screen where the next chapter shows below the hero (at 1440×1300 and 1920×1600).
