# Changelog

All notable changes to the finstory plugin are recorded here. The version is the one in `.claude-plugin/plugin.json`.

## 1.0.3 (2026-09-30)

- `board-story`: works when a story page comes back without the report image, as it does on a host without MCP Apps such as Claude Code. The page notes decide: when the user is shown the rendered report, Claude writes around it as before; when no image came with the result, Claude says the report image isn't shown in this app, shows the page's headline figures and the rows its questions cite as one small table, and writes from those figures without describing a picture. The pack's "No image" notice now separates an image never rendered for the connection from one that was too large or failed to render.
- `financial-questions` and `report-builder`: an account of type Balance (a level such as headcount) shows its level in every view, so a quarter or year shows its latest month and a rolling-average view the average level; a level held in another account type of a scenario stored year to date is still read at year to date. Levels are never added up over periods.
- README: the privacy link points at the "finstory in Claude" section of the privacy policy, the install section says the repository is public and is its own marketplace, and the `board-story` row says what happens without the report image.
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
