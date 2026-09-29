# Reading finstory analysis results

The `tool_notes` returned by the finstory connector's `company_get_context` tool (job `analyse`) describe the query templates and result fields in full. These are the points that most often change what the answer says. Every tool named here is a finstory connector tool.

## Figures and formatting

- An `analysis_query` result carries `table`, `rowCount`, `formatting` and `warnings`.
- Format every figure the way `formatting` says: scale, decimals and the variance sign convention. Don't print raw floats, and show percentages as percentages.
- Name a currency only when the query's scope sets one, through `pov` or the context's default scope. Without one, `formatting.currency` holds a fixed default, not the workspace's currency, so leave the currency out.
- An empty variance-% cell is deliberate: the comparison is absent, zero or tiny next to the figure, or the two sit on opposite sides of zero. Quote the absolute variance and leave the percentage out rather than working it out yourself.

## Warnings and empty results

- Read `warnings` before any conclusion that rests on an affected figure.
- `PLAN_MISSING` names a row whose comparison (budget, forecast or the earlier year, as labelled) is absent, zero or under 1% of the actual. With no material comparison, the row's whole actual shows as its variance and no percentage applies. The line still moves the total, often more than any other line, so keep it in rankings, bridges and the reading, labelled "unbudgeted" (or "not in the forecast", "not in the prior year"). Before calling it an overrun or a shortfall, check whether its comparison sits on a sibling line or its parent (one breakdown of the parent against the comparison shows it), and say which it is: a line with no budget anywhere, or one budgeted on another line.
- Warnings cover only the cells this query returned. An empty list means nothing was flagged in what you asked for, not that the data is clean.
- If a query returns no values, re-run that one query with `verbose: true` and read the generated query before telling the user the data is absent. Leave `verbose` off otherwise; it makes the result several times larger.

## Members and KPIs

- Where a margin, ratio or cycle exists in the KPI library, query that calculated member directly, so the figure matches the company's own formula.
- Member lists in the context can be truncated. Search with `company_find_members` before saying a member doesn't exist.

## Dimension scope

Some breakdown dimensions carry data only for one branch of the accounts (their top account), for example a channel split that exists only down to gross profit.

- A breakdown outside that branch is refused with a suggestion; follow the suggestion.
- For an account above the branch, the result comes back with a scope warning. Either report the catch-all member explicitly ("unallocated") or query the top account instead, and say which you did.

## Models and levels

- With two or more models, pass `model` on every call. One query reads one model.
- A level such as headcount is read at year to date: the periodic view of a level shows the change, not the level. Never add a level up over periods.
