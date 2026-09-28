# Changelog

All notable changes to the finstory plugin are recorded here. The version is the one in `.claude-plugin/plugin.json`.

## 1.0.0 (2026-09-28)

First release.

- Bundles the finstory connector (`https://mcp.finstory.ai/mcp-finstory`) through `.mcp.json`.
- Adds the `financial-questions` skill: answers questions about actuals, budget and forecast with a headline, one visual, a short reading of the drivers and lettered next steps.
- Adds the `report-builder` skill: designs, builds, previews and publishes finstory reports, and edits existing ones.
- Adds the `board-story` skill: builds finstory board stories one page at a time, rolls a story forward to a new period, and exports a story for other formats.
- Moves the workflow and presentation guidance that the connector used to return into these skills, so the connector describes only its tools.
