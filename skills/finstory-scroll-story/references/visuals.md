# Choosing visuals

> **finstory draws seven of these types: bars, hbars, columns, waterfall, line, table and cards.** Stacked, meter,
> heatmap, scatter, timeline and html are refused on save; use the mapping below to pick one of the seven. The
> snippets name the old `storylib` helpers only to show the shape: write the fields as JSON, with figures as
> references (reference-format.md).

The engine draws thirteen visual types. Most finstory pages map onto one of them directly; this guide covers that
mapping, the data shapes that need a decision, and the edge cases that break a naive translation. Snippets use
`storylib` (spec.md has every field).

Contents: 1 Principles · 2 finstory component → visual · 3 Data shape → visual · 4 Edge cases · 5 Chart types the
engine doesn't have · 6 The html escape hatch · 7 Before you build

## 1 · Principles

- **The data's job picks the visual**, not the finstory chart type. Ask what the step claims: a change over time,
  a bridge from A to B, which items moved, a share of a whole, a relationship between two measures, one figure
  against its comparisons, progress to a goal. Section 3 maps each job to a type.
- **One axis per chart.** Two measures in different units (revenue in $ and margin in %) are two visuals in the same
  chapter, and the steps switch between them. Never fake a second axis by rescaling.
- **Colour means something.** Blue is this year (`focal`), grey last year (`context`), the dark tick budget
  (`marker`), light blue a forecast (`soft`). Green and red only ever mean better and worse. Segments use `s1`–`s5`
  in that order, and the same entity keeps the same colour across visuals.
- **A step must change the picture**: another visual, a highlight (`rows`, `cats`, `series`), a waterfall leg revealed.
  A visual nobody points at, or four steps on an unchanging chart, is a sign the chapter needs restructuring.
- **Fewer, bigger marks.** Twelve months, eight rows, seven bridge legs, five segments: beyond that, fold the tail
  into "Other" and put the full list in the appendix.
- **Values in the chart's unit.** `unit: "k"` takes thousands (`k1(dollars)`), `"m"` millions (`m2(dollars)`),
  `"pct"` fractions (0.394). Pick `k` for figures up to about $50M and `m` above; keep one unit per chapter.

## 2 · finstory component → visual

`story_get(view:"structure")` gives each page's title, subtitle, chips and annotation targets; the display pack gives
its components (KPI cards, charts, tables) and their figures.

| finstory component | visual | notes |
|---|---|---|
| KPI cards | chapter `kpis` (`kpi(...)`) | up to 4; steps highlight them with `kpi=` |
| Waterfall / bridge | `waterfall` via `bridge()` | cost-only bridges: `invert=True`; fold beyond 7 legs |
| Margin walk (margin from A to B, by driver) | `waterfall` with `unit="pct"`, `legUnit="pts"` | totals read 28.6%, legs +2.3 pts |
| Column chart over months | `bars` | budget or last year as `marker`, or as a second series |
| Line chart over months | `line` | ratios: `zeroBased=False`; forecast: `dashedFrom` |
| Combo (bars + line on two axes) | `bars` and `line`, two visuals | the line usually carries a % (margin, growth) |
| Stacked columns by segment | `stacked` | `mode="share"` for mix, `"abs"` for totals; max 5 segments |
| Pie or donut | `hbars` sorted, `unit="pct"` | or `stacked` share with one column when 2–4 parts |
| Variance table, B/(W) by row | `hbars` with `mode="variance"` | sort `"abs"`; cost rows: B/(W), not the raw change |
| Actual vs budget vs last year of one figure | `columns` with `delta` | three columns, bracket between two |
| Gauge, progress to target | `meter` | `goal` for the target, `marks` for budget and last year |
| Bullet (actual vs target per row) | `hbars` with `compare` | the tick is the target |
| Two periods across named items (channels, stores) | `hbars` with `compare` (this year bar, last year tick) | reads better than two-series bars with long names |
| P&L or KPI table | `table` with `pl_row()` / `ratio_row()` | wide ones go to the appendix |
| Matrix of variances (lines × months, stores × lines) | `heatmap` | diverging; `invert` on cost rows |
| Scatter or bubble | `scatter` | `size` for bubbles, `group` for colours |
| KPI grid with sparklines | `spark_tiles()` | up to 12 tiles |
| Text block, priorities, risks | `cards` | 2–4 cards; steps highlight with `item=` |
| Roadmap, dated milestones | `timeline` | steps move through with `phase=` |
| Annotations (pins on cells) | step text plus a highlight | annotations are the best source of steps |

## 3 · Data shape → visual

| the step is about | use | storylib |
|---|---|---|
| a monthly series, this year vs budget | `bars` with a budget marker | `bars(MONTHS[:5], [("2026", vals)], "k", marker=("Budget", bud))` |
| this year vs last year by month | `bars`, two series | `bars(cats, [("2025", ly), ("2026", act)], "k")` (roles default to grey, blue) |
| a trend over 12–36 months | `line` | `line(cats, [("Revenue", vals)], "m")` |
| a ratio over time (margin, DSO) | `line`, not zero-based | `line(cats, [("Margin", fr)], "pct", zeroBased=False, refs=[{"value": 0.316, "label": "Budget 31.6%"}])` |
| actual months then forecast | `bars` with `forecastFrom` or `line` with `dashedFrom` (both per series) | `bars(cats, [{"name": "2026", "values": vals, "forecastFrom": 6, "forecastLabel": "Reforecast"}], "m")` |
| what bridges A to B | `waterfall` | `bridge(("Budget", k1(b)), legs, ("Actual", k1(a)), "k", vsLabel="budget")` |
| how a margin moved, driver by driver | `waterfall`, totals in %, legs in pts | `bridge(("May 2025", 0.286), [("Staff", 0.023, "Staff"), ...], ("May 2026", 0.330), "pct", legUnit="pts")` |
| which items beat or missed | `hbars` variance | `hbars([(name, k1(v), overunder(chg))], "k", mode="variance", sort="abs")` |
| the size of items, ranked | `hbars` value | `hbars(rows, "k", sort="desc", total=("Total", k1(t)))` (the total is printed, not drawn) |
| the mix (shares of a whole) over time | `stacked` share | `stacked(cats, [(seg, vals)], mode="share")` |
| one figure against two comparisons | `columns` | `columns([("2025", ly), ("Budget", b), ("2026", a)], "m", delta=(1, 2))` (for a % the pill reads in pts) |
| progress to a goal | `meter` | `meter(("Actual", 0.33), (0.25, 0.40), marks=[("Budget", 0.316)], goal=("Target", 0.35))` |
| how two measures relate across items | `scatter` | `scatter([(name, growth, margin, size, group)], "pct", "pct")` |
| a variance for every item and month | `heatmap` | `heatmap(MONTHS[:9], [(line, vals, is_cost)], "k", mode="diverging")` |
| many KPIs, each with a trend | `spark_tiles` | `spark_tiles([(label, series, "38.9%", "+0.6 pts", "good")])` |
| figures to look up, not compare | `table` | `table(cols, [pl_row(...)], sign_cols=[2, 3])` |
| reasons, priorities, risks | `cards` | `cards([("01", "Price", "…"), ("02", "Mix", "…")])` |

## 4 · Edge cases

**Bridges**
- *Profit bridge from budget or last year*: income legs are actual − comparison, cost legs comparison − actual. Use
  `bw(actual, comparison, cost=True)` for costs so a saving is a positive (green) leg.
- *Cost bridge* (opex budget → actual): legs are actual − budget per cost line and `invert=True`, so an overrun is
  an up leg in red and the pill reads red when total costs rose.
- *More than 7 legs*: `fold(legs, keep=6)` keeps the six largest (in their order) and folds the rest into "Other";
  give every leg a `short` name (≤10 characters) for phones.
- *It doesn't close*: a line is missing (often "Other" or an unbudgeted line such as interest). Add it. `bridge()`
  pushes small rounding into the largest leg by default; `plug=None` raises instead, so real gaps surface. Use
  `allowGap` only when the source report itself doesn't close, and say so in the data notes.
- *Small legs on big totals*: the engine cuts the axis of money bridges automatically, with break marks and an "Axis
  starts at" note. Set `axisFrom=0` when the totals' sizes against each other are the finding (net debt up 58%, a
  margin halving), since a cut exaggerates the change. Ratio bridges start at zero unless you zoom with `axisFrom`. *Negative start or end* (a loss): fine, the axis goes below zero.
- *A bridge per lens option*: build each with its own comparison and wrap them, `LZ(bridge_from_budget,
  bridge_from_last_year)`; give their steps lens-aware `reveal` and `rows` when the legs differ.
- *A margin walk*: margins don't add like money, but their point changes do: totals in `pct` (fractions), legs in
  points (fractions too) with `legUnit="pts"`, so the start reads 28.6%, a leg +2.3 pts and the pill +4.4 pts.
- *On phones* a bridge with 7 or more columns, or whose labels would be cut, turns sideways by itself (labels on the
  left, values on the right). Short forms still help: `short` on legs, start and end.

**Signs and percentages**
- B/(W) convention: positive is better. For a cost line the B/(W) is comparison − actual, so an overrun is negative.
  Beside a variance bar, describe a cost's change in words, `overunder(0.047)` → "4.7% over", never "+4.7%" next to
  a red bar.
- A % change on a small, zero or opposite-sign base is not meaningful: `pcell()` returns `n.m.` above 200% and blank
  when the base flips sign or is tiny. In text, say "turned positive" or give the amounts.
- Differences between two percentages are points: `pts(0.014)` → "+1.4 pts", unit `"pts"` in charts.
- Margins and shares go in as fractions with `unit="pct"`. Never multiply by 100.

**Missing and partial data**
- A comparison that exists under one lens option only (a budget series but no last-year one): `None` in `LZ()` for
  the marker or the KPI delta (`marker=LZ(("Budget", bud), None)`, `kpi(..., delta=LZ("+5.0%", None))`), or two
  different visuals in one slot (`LZ(ebitda_bridge, gross_profit_bridge)`). Say in the step what the other view lacks.
- No budget for a measure (cash flow, balance sheet, KPIs): plain values, not `LZ`; compare with last year or the
  prior month, and the chapter reads the same under both lens options. A story with no budget at all has no lens.
- No budget for the year ahead (an outlook story): the plan to compare with is the latest management forecast or
  reforecast; relabel the switch rather than calling it a budget (references/full-build.md, section 6).
- Months not yet closed: `None` in a line ends it; bars skip it. Don't plot zeros for months with no data.
- A placeholder in the data (a flat figure repeated, "Item_8", a member with a garbled name): leave it out of the
  chart, keep it in the appendix if useful, and name it in the data notes.
- A dimension that doesn't add up to the total (members missing from the query): show the members you have and an
  "Other / unallocated" row for the gap, so totals still tie.

**Labels**
- Bars: `labels="auto"` labels the focal series' max and last when crowded; `"all"` labels every bar of every
  series; `"focal"` every bar of the focal series. Two different measures in one bar chart (EBITDA and net profit)
  only label one of them: draw one chart per measure.
- Line: `"auto"` labels the max and the latest point and drops labels that would collide; `"all"` labels every point
  of the focal series; a `callout` on a step replaces the label at its point. Use callouts to tell the story month by
  month.
- Text and chart agree when chart values keep full precision (`k1()`, `m2()`) and text uses `money()` / `kd()`:
  both round the same way. Rounding chart values yourself makes "+714" sit next to "$713K".

**Crowding**
- Long names: every label that can be cut takes a short form, used where the full one would be cut and then by
  every sibling, desktop included, so it must read well alone ("WHLSUP", not "WHLSUP BS"):
  `categoryShort=[...]` for bars, line and stacked; `short` on waterfall legs, hbars rows, columns items and scatter
  points (`shorts={"Marketplace & payment fees": "Fees"}` on the helpers). `check` lists each label it cut. The
  tooltip, the Data table and the appendix keep the full names.
- More than 12 categories: bars thin their labels automatically; for 24–36 months prefer `line`.
- More than 8 rows: top 7 by size plus "Other" in the chart and every row in the Data table:
  `top_hbars(rows, n=7, other='{k} other lines')`; the appendix only when the list is a finding in itself.
- More than 5 segments: fold the smallest into "All other" (the stacked chart warns above 6).
- More than ~8 scatter points: label only what the steps discuss (`labels="none"` plus `rows=` on steps); the
  extremes are labelled automatically.

**Structure**
- One number only: a KPI tile or the hero stat, not a chart.
- Two figures (actual vs budget): `columns` with `delta`, or a KPI tile with a delta.
- `columns` with a `delta`: order the items so the two compared sit side by side (`[last year, budget, forecast]`,
  `delta=(1, 2)`); a pill that spans a taller column covers its label.
- A text-only page (strategy, risks, priorities): `cards`, and `timeline` when the items are dated.
- Scenarios (base / upside / downside): `columns`, or a lens when each scenario changes the whole story.
- Different bases on different pages (consolidated vs unconsolidated, reported vs adjusted): a basis lens with
  `lens_map(rep=..., adj=...)`, or say which basis each chapter uses in its eyebrow and panel chip.
- Month and year-to-date on one page: one visual each, and the eyebrow or panel chip names the period.

## 5 · Chart types the engine doesn't have

| asked for | draw instead | why |
|---|---|---|
| dual-axis combo | two visuals, one per measure | one scale per chart reads honestly |
| pie / donut | `hbars` sorted by share (or one-column `stacked`) | lengths compare better than angles |
| stacked area | `stacked` (abs) by month, or `line` per segment | same data, readable values |
| funnel | `hbars` value mode, in stage order (no sort) | each stage's drop is a bar length |
| treemap | `hbars`, top 7 plus "Other" | sizes compare on one axis |
| gauge with zones | `meter` with `marks` and `goal` | zones become reference marks |
| radar / spider | `hbars` per dimension, or `table` | radial scales distort |
| histogram | `bars` over value buckets | |
| map by region | `hbars` or `heatmap` by region | |
| sparkline table | `spark_tiles` or `table` + a `line` visual | |
| Sankey, org chart, process flow | `html` (section 6), or `cards` | |

## 6 · The html escape hatch

For a picture no type covers (a flow diagram, an org chart, a custom infographic). Rules:
- Lay text out in HTML with CSS grid or flex; keep SVG for shapes only. SVG text scales with the panel and becomes
  unreadable on a phone.
- Colour with the theme variables (`var(--focal)`, `var(--focal-soft)`, `var(--context)`, `var(--ink)`,
  `var(--ink-2)`, `var(--muted)`, `var(--green-mark)`, `var(--bad-mark)`, `var(--line)`, `var(--surface)`) so dark mode
  works. No scripts (the build refuses them); `esc()` any text that comes from data.
- Give it `table={"head": [...], "rows": [[...]]}` so the Data button shows the figures.
- Keep it under about 380px tall on desktop, and look at the phone screenshot.

A tested pattern, a proportional flow from sales to EBITDA:
```python
FLOW = [("Gross sales", 13.01e6, "--context"), ("Net revenue", 12.70e6, "--focal-soft"),
        ("Gross profit", 4.19e6, "--focal"), ("EBITDA", 1.32e6, "--green-mark")]
markup = '<div style="display:grid;gap:10px">' + "".join(
    f'<div style="display:grid;grid-template-columns:minmax(84px,26%) minmax(0,1fr);align-items:center;gap:10px">'
    f'<span style="color:var(--ink-2)">{esc(label)}</span>'
    f'<div style="display:flex;align-items:center;gap:8px">'
    f'<i style="display:block;height:26px;width:{v / FLOW[0][1] * 78:.1f}%;background:var({c});border-radius:6px"></i>'
    f'<b style="color:var(--ink);font-weight:600;white-space:nowrap">{money(v)}</b></div></div>'
    for label, v, c in FLOW) + '</div>'
visual = html(markup, title="May · from sales to EBITDA", table={"head": ["", "May"], "rows": [[l, money(v)] for l, v, c in FLOW]})
```
`spark_tiles()` is the same mechanism, packaged: tiles in HTML, only the line in SVG.

## 7 · Before you build

- Does every chapter have one visual per claim, and every step a change on screen?
- Does each second or third visual in a chapter add a measure, a time span or a breakdown, not the same rows against
  another comparison? Do months, bridges and ratios appear where the findings call for them?
- Is each comparison named for what it is (budget, last year, the reforecast, the 2025 run rate)?
- Are cost lines in B/(W) terms, and their notes in words?
- Is anything crowded: over 7 legs, 8 rows, 12 categories, 5 segments, long labels without short forms?
- Do two units share a chart? Split it.
- Under the lens, does each visual change to the right comparison, and do the step texts follow?
