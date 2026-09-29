# Visuals for a finstory answer

## Format

For a finstory answer an inline table or chart usually works best: the user sees the finding in the conversation without opening anything. If the host has a built-in way to draw tables and charts inline, it suits this well; a markdown table is the plain fallback.

If the user asks for a file, a spreadsheet, code, a dashboard or an artifact, provide that instead. Their request decides the format, and the preferences below still help with what goes into it.

If a visual fails to draw, retry once, then give the same content as a markdown table. A complete plain answer beats a half-drawn one.

## How much to draw

- At most two visuals per answer, and at most one of them a chart.
- A tile row above a table counts as one visual.
- Small multiples of one series (up to three panels in a row, on one shared scale) count as one chart, with the bar limit applying per panel.
- Anything beyond that becomes a "Where next?" option rather than another visual.

## What to draw

- **Tile row**: the two to four numbers the answer turns on.
- **Styled table**: at most eight rows plus a total, and five columns. Show the rows the answer turns on, not every member.
- **Horizontal diverging bar** around a zero baseline, at most ten bars, ranked by size: the drivers of a variance. This works as the budget-to-actual bridge; where the host draws a proper waterfall, that is fine too.
- **Over time**: the same diverging bar with periods on the axis, showing the gap per period as one series. That reads faster than actual and budget as two grouped bars the reader has to subtract.
- Avoid pie charts and charts with only two points; a tile or a sentence says it better.

## Colour by favourability, not by sign

Variance is base minus comparison, so a positive variance on a cost line is adverse.

- The result's `formatting.favourability` says, per group of accounts, what a positive variance means; the tool notes from the finstory connector's `company_get_context` tool describe it.
- Where a group has a type but no direction, read the direction from the type and the stored sign (a cost stored negative improves toward zero) and say in a short clause how you read it.
- Where the result has no `formatting.favourability` at all (the query named no account, or spans a long list of accounts, as a leaf-level breakdown does), take each account's direction from its `profitSign` in `company_find_members` or the context listing (a listed member without one takes its parent's). With actual minus plan, or later minus earlier, 1 means a positive variance is favourable and -1 adverse; flip it when the variance runs the other way. Without a `profitSign`, use `accountType` and the stored sign; with neither, use the neutral colour. Say in a short clause that you read the direction from the account model.
- Balance-sheet lines and ratios get a neutral colour.
- On a bridge that mixes income and cost lines, plot profit impact: flip the lines marked adverse, so that every bar on the favourable side helped profit.
- Use one colour for favourable and one for adverse throughout the answer, and label the direction in words as well as colour.
