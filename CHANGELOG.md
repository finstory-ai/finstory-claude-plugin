# Changelog

All notable changes to the finstory plugin are recorded here. The version is the one in `.claude-plugin/plugin.json`.

## 1.0.15 (2026-10-18)

- `finstory-scroll-story` works lean: SKILL.md is short enough for a host to read whole, chapter design moves to `references/design.md` and the writing rules to `references/writing.md` section 0, and each reference is read once, at the step that needs it. The skill loads its tools in one search, calls `company_get_context` once, saves three or four chapters per `story_update` (`scroll.chapters`) with the hero, appendix and notes in the last save, writes both switch positions before saving, files each answer under the chapter that cites it, reviews with the save replies (`charts`, `repeatsHero`, `stepsWithoutBasis`) instead of reading the story back, and tells the user once that "Always allow" on the Story tools avoids a prompt per save. Design adds a mix angle and when to use statement, cards and timeline chapters; writing adds shares above 100%. Needs the finstory connector with `scroll.chapters` (KAN-269).

## 1.0.14 (2026-10-17)

- `finstory-scroll-story`: drill to a detail chapter. A bars category (`drill[]`, one per category), an hbars row or a waterfall leg may name the chapter that details it (`drill`); tapping its figure opens the source card with "Open detail: <chapter> ›", which goes there. A drill to no chapter of the story is refused at save. Needs the finstory app and connector with drill (KAN-266).

## 1.0.13 (2026-10-17)

- The manifest drops the directory listing keys (`icon`, `privacyPolicyUrl`, `termsOfServiceUrl`, `supportUrl`). claude.ai showed every customer who synced the plugin since 1.0.9 a warning that these keys are unrecognized and stripped. The directory listing keeps the icon and links it read when the plugin was submitted. No skill changes.

## 1.0.12 (2026-10-16)

- `finstory-scroll-story`: tables and tiles. A table can group columns under a header (`groups`) and give a row an accent band (`tone`); KPI tiles take a second line (`sub2`), sit in labelled rows (`row`) and may write out a goal from a recorded answer they cite (`basis`); text figures format in basis points (`bps`). Needs the finstory app and connector with these (KAN-262).

## 1.0.11 (2026-10-16)

- `finstory-scroll-story`: more chart forms. `stacked` is drawn as a mix (each column 100%, its total above, dashed connectors between segments); a line series can be `dash`ed or dotted throughout, or dashed from a category on (`dashedFrom`, a projection); a line chart can shade `bands` between two figures from a category on, labelled inside, above and below; a band or reference line (a goal) may take its numbers from a recorded answer it cites (`basis`). Needs the finstory app, document service and connector with these charts (KAN-260).

## 1.0.10 (2026-10-16)

- `finstory-scroll-story`: text-only chapters. A chapter with no visuals is a statement (one large phrase per step, building up as the reader scrolls) for the headline message and section openers; cards take a coloured accent (`tone`) and a figure (`value`) and light up per step (`item`); `timeline` is drawn, moving through its phases per step (`phase`) with a "Today" mark. The line `dashedFrom` and `area` fields, which finstory refuses, are no longer recommended. Needs the finstory app and document service with text chapters (KAN-258).

## 1.0.9 (2026-10-15)

- Listing assets for the Claude Directory: the finstory icon (512×512) and the privacy policy, terms of service and support URLs in the plugin manifest. No skill changes.

## 1.0.8 (2026-10-13)

- `finstory-scroll-story`: the review pass reads the chart mix finstory now returns on every save (`charts` per type, `repeatsHero`) and redesigns the chapters that push a chart type past four or draw the hero chart again. Needs the finstory connector with the chart mix in its save reply.

## 1.0.7 (2026-10-13)

- `finstory-scroll-story`: answers are recorded in one call (`scroll.answers`), and a step that cites an answer writes out the user's own figures, never the process behind them. The story holds one frame, puts the bridge before the chapters that explain its legs (never drawing the same legs twice), keeps each chart type to four uses and the hero's chart out of the chapters, and reads right under both switch positions. Headlines carry a verb and a cause; movement verbs need a material figure and comparisons both sides. Variance lists sort best to worst, axis cuts only for small changes, and a stock build in a cash or capital bridge reads as a use of cash. A last review pass checks all of this before the reply. Needs the finstory connector with `scroll.answers`.

## 1.0.6 (2026-10-13)

- `finstory-scroll-story`: each chapter argues one finding and shows it from the angles that prove it (the period against its comparisons, the trend by month, the breakdown, a ratio, a bridge, the effect on cash or the year), mixing chapters that walk one chart part by part with chapters that show one issue in two or three charts. A second chart must add a measure, a time span or a breakdown, not the same rows against another comparison. Chapters follow the findings and use the workspace's analysis reports and any earlier finstory story the user names; the draft is scaffolding, and the number of chapters follows the scope.

## 1.0.5 (2026-10-12)

- New skill `finstory-scroll-story`, until now shared as a separate zip: builds an interactive scroll story in finstory. Claude agrees an outline from the request, the data, the company context and last period's story; finstory drafts it without AI (`story_create` with `draft`); Claude asks at most three questions per chapter about what finstory's evidence shows stands out, judged against each line's usual monthly swing rather than a fixed percentage; records the user's answers with `story_update` (`scroll.answer`), stamped by finstory with who and when; and writes the words chapter by chapter, giving every step a basis: the data, the user's answer, or an assumption. Readers see that basis when they switch Basis on. Needs the finstory connector with scroll-story answers, evidence and drafts.

## 1.0.4 (2026-10-10)

- `board-story`: notes on a page are numbered 1, 2, 3 (or A, B, C) in reading order, with what each note is about in its title. A note's badge is drawn in a small circle on the report, where a word such as "EBITDA" was cut off and covered the figure; the finstory connector now refuses a badge longer than two characters.
- `board-story`: when the user shares a slide to recreate, Claude matches each chart and table to a report and offers to build a missing one with the report-builder skill, rather than typing its figures into a text block that goes stale when the story rolls forward. It checks the basis the slide states (consolidated or not, the period) before saying the figures match.
- `report-builder`: a waterfall can bridge by P&L line (budget EBITDA through revenue and cost lines to actual EBITDA), built as a fixed list of accounts named by their account codes so costs subtract.
- `report-builder`: when the user shares a picture of a report to rebuild, Claude first writes down everything it shows (every component, table row, style and figure), builds it part by part (handing parts to sub-agents where the host offers them), then compares the rendered preview with the picture and fixes differences, for up to three rounds.

## 1.0.3 (2026-09-30)

- `board-story`: works when a story page comes back without the report image, as it does on a host without MCP Apps such as Claude Code. The page notes decide: when the user is shown the rendered report, Claude writes around it as before; when no image came with the result, Claude says once that the report image isn't shown in this app, shows the page's headline figures and the rows its questions cite as one small table on each page, and writes from those figures without describing a picture. The skill also handles the notice that no image was rendered with the result and that the story-page app loads it itself, which can come with any result on any host: Claude doesn't call the image missing, and writes around what the app reports showing. An image the app could not show, and charts that failed to draw on it, are taken from the app's own update; the cases an earlier connector version sends (no notice, or a notice in the pack that the image was too large or could not be rendered) are kept. The page notes in each result decide over the connector's session tool notes.
- `financial-questions` and `report-builder`: an account of type Balance (a level such as headcount) shows its level in every view, so a quarter or year shows its latest month and a rolling-average view the average level; a level held in another account type of a scenario stored year to date is still read at year to date. Levels are never added up over periods.
- README: the privacy link points at the "finstory in Claude" section of the privacy policy, the install section says the repository is public and is its own marketplace, the `board-story` row says what happens without the report image, and the data section says who sees a new report and that changes to a published report or shared story show straight away.
- SECURITY.md: points to the reporting instructions on the security page rather than to a form, since the page has none.

## 1.0.2 (2026-09-29)

Review fixes to 1.0.1.

- `financial-questions` and `board-story`: "last month" and "last closed month" never pick the current calendar month, which may still be loading, so a part-month is not set against a full-month budget. Only "this month" reads the current month, and the answer says it is not closed. The board-story lookup passes `model` on a workspace with several models.
- `financial-questions`: a `PLAN_MISSING` line stays in rankings, bridges and the reading, labelled unbudgeted, because it still moves the total; before calling it an overrun, Claude checks whether its budget sits on another line. A currency is printed only when the query's scope sets one.
- `board-story`: a month or column missing from a display pack table may have been cut to fit, so Claude asks for it rather than saying the report lacks it. When a rolled-forward page keeps the old period, Claude checks the page's own scope and a `"@default"` story period before saying the report fixes its period.
- `report-builder`: the metrics-by-month principle points to `report_build_list` and `report_describe_schema` for rolling lists.
- `financial-questions` (8b193ea, added after the version bump under the same number): the account-model favourability fallback applies only when a long list of accounts spans more than one group; a wide query within one group keeps the result's own favourability statement.

## 1.0.1 (2026-09-29)

Fixes from the first live test of the skills.

- `financial-questions`: a relative period ("last month", "last closed month") now starts from the latest month with actuals rather than the workspace's default period. A subtotal the user names but the model lacks is searched for, and the answer names the line it used instead of adding accounts together. Favourability can now be read from the account model when a result carries none, and a `PLAN_MISSING` row is described as unbudgeted rather than as a driver. The worked example prints figures the way a result's `formatting` gives them.
- `report-builder`: preview values are quoted in the report's number format rather than as raw floats. After publishing, the report's menu group and link come from the tool results. The design principles and the model layout description turn a metrics-by-month table around so its months can roll.
- `board-story`: a relative period is proposed from the latest month with actuals. When no report fits a page, the skill offers to build one with report-builder and return. Rolling a story forward now also checks tags, text blocks and notes for stale wording, and flags pages whose report fixes its own period. The story link is used only when a tool returned it. Display pack notices distinguish components that could not be read from components beyond the summary's limit. A long export falls back to one page at a time when a batch is still too large for the host.

## 1.0.0 (2026-09-28)

First release.

- Bundles the finstory connector (`https://mcp.finstory.ai/mcp-finstory`) through `.mcp.json`.
- Adds the `financial-questions` skill: answers questions about actuals, budget and forecast with a headline, one visual, a short reading of the drivers and lettered next steps.
- Adds the `report-builder` skill: designs, builds, previews and publishes finstory reports, and edits existing ones.
- Adds the `board-story` skill: builds finstory board stories one page at a time, rolls a story forward to a new period, and exports a story for other formats.
- Moves the workflow and presentation guidance that the connector used to return into these skills, so the connector describes only its tools.
