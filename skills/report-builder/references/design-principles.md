# Design principles for finstory reports

The finstory connector's `report_design_brief` tool returns layout patterns and notes for the report type it detects (statement, KPI dashboard, variance review, composition, ranking, trend, operational). Start from those. The principles below hold for every type; the exact parameter rules are in the tool notes from the finstory connector's `company_get_context` tool and are not repeated here.

## Every report

- **One model, one purpose.** A report reads one model. Decide what the reader should learn from it before choosing components.
- **Scope lives in the header.** Entity, year, period and view selectors belong in the report header, not in a table cell.
- **Rows and columns vary over different things.** Accounts down the side and periods or scenarios across the top is the usual shape. Putting the same kind of thing on both axes doesn't render.
- **Let hierarchies supply the members.** Resolve the dimension and build the rows from its tree, rather than typing out members by hand, so the report keeps working when members are added. A time axis works best as a rolling list that moves with the data.
- **Subtotals are decisions.** Gross profit, EBITDA and operating income are calculated lines you define, not members you look up. Check the sign convention before writing one: where costs are stored as positive numbers, gross profit is revenue minus cost, not their sum.
- **Percentages are proportions.** A ratio or relative-variance column is formatted as a percentage automatically; don't give it the report's thousands scale, which would show 0.229 as 0.
- **Share of a reference.** A "% of revenue" column is a normal part of a statement: a hidden column fixed to the reference account plus a ratio column against it. The tool notes explain the mechanics.
- **Variance columns show direction.** Use coloured figures when the reader needs both the size and whether it is favourable; use traffic lights when direction alone is enough.
- **Levels are not flows.** A level such as headcount is never summed over periods. An account of type Balance shows its level in any view, so its quarter or year column shows the latest month. A level held in another account type of a scenario stored year to date is read at year to date, so a report that mixes it with a flow puts the view in its columns: year to date for the level, periodic for the flow.

## Choosing a visual

| The question | Visual |
| --- | --- |
| How did it move over time? | Line (one series); grouped bars to compare scenarios |
| Did we beat or miss, period by period? | Variance area, rather than two lines on one chart |
| What drove the change from A to B? | Waterfall, with the legs built from a breakdown dimension |
| What share does each part have? | Pie up to about seven slices; beyond that go up a level or use a ranked bar |
| How far are we toward a target? | Gauge tile |
| Who are the top performers? | Rank tile, rolling the rest into "Others" |

## By report type, in brief

- **Statement** (P&L, balance sheet, cash flow): a fixed, ordered list of accounts with calculated subtotals. Size the columns to the width: at full width Actual, Budget, Variance, Variance %, YTD and Prior YTD; in a narrower slot Actual, Budget, Variance and Variance %. If the company has no budget, use Actual, Prior year and Variance.
- **Metrics by month, actual against budget** (a few metrics such as revenue, gross margin and EBITDA): a rolling list of months can't also carry the scenario, so turn the table around. Put the rolling months down the side, and across the top a fixed list of columns over account and scenario: each metric's actual, budget and variance. A calculated metric such as EBITDA becomes a calculated column over earlier columns; its ingredients for each scenario go in ahead of it as hidden columns. The tool notes explain hidden and calculated columns; `report_build_list` and `report_describe_schema` describe the rolling list, which varies over year and period together.
- **KPI dashboard**: pick the tile type per metric (target, trend, single comparison, driver walk, top-N) rather than six identical variance tiles. Give each rank tile a different breakdown. Four to six tiles read best as two even rows.
- **Variance review**: lead with the drivers of the gap; follow with the line-by-line table.
- **Operational** (headcount, FTE, pipeline): counts and levels, not money. Ask the user for the number format.

## Keep it to what was asked

A report the user described should contain what they described. Extra tiles, charts or columns are suggestions for the conversation, not additions to the build.
