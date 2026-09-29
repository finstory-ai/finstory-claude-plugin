# Changelog

All notable changes to the finstory plugin are recorded here. The version is the one in `.claude-plugin/plugin.json`.

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
