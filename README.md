# finstory plugin for Claude

The finstory plugin for Claude, from Finstory, Inc. It connects Claude to your company's finstory workspace so you can ask questions about your actuals, budget and forecast, build and edit finstory reports, and draft board stories page by page, all from a Claude conversation.

The plugin bundles two things:

- **The finstory connector**, a remote MCP server at `https://mcp.finstory.ai/mcp-finstory`. Its tools read your finstory data and, when you ask, create or change reports and stories in your finstory workspace. It works within your own finstory role and permissions.
- **Three skills** that tell Claude how to work with finstory: how to answer a question about the numbers, how to build or edit a report, and how to build a board story.

It works in Claude on the web and desktop (chat and Cowork) and in Claude Code.

## Requirements

- A finstory subscription, and a finstory login with access to your company's workspace.
- A paid Claude plan.
- In claude.ai and the desktop app, skills need **Code execution and file creation** switched on in Claude's settings. The finstory skills themselves run no code; the setting is what lets Claude load skills.
- On a Team or Enterprise plan, an Owner adds or allows the finstory connector for the organization.

## Install

This repository is public and is also a plugin marketplace named `finstory`, so you add it by its GitHub name, `finstory-ai/finstory-claude-plugin`.

### Claude on the web and desktop (chat and Cowork)

1. Go to **Customize > Plugins > Add**.
2. Choose **Add marketplace** and enter `finstory-ai/finstory-claude-plugin`, then install **finstory**. Alternatively, choose **Upload plugin** and upload a zip of this repository.
3. Once the plugin is listed in the Claude Directory, you can also install it from there.

On a Team or Enterprise plan, an Owner can install it for the organization from **Organization settings > Plugins**.

### Claude Code

```bash
claude plugin marketplace add finstory-ai/finstory-claude-plugin
claude plugin install finstory@finstory
```

### Connect finstory

After installing, open the plugin's **Connectors** tab, connect **finstory**, and sign in with your finstory login at `app.finstory.ai`. In Claude Code, run `/mcp` and authenticate the finstory server. If Claude says the finstory tools are not available, this step has not been completed yet.

## Skills

| Skill | What it does |
| --- | --- |
| `financial-questions` | Answers questions about your numbers: variances against budget or prior year, cost lines, margins, trends and KPIs. Replies with a headline, one visual, a short reading of the drivers, and lettered options for the next cut. |
| `report-builder` | Designs a finstory report with you, builds it as a hidden draft, previews the figures, and publishes it only when you agree. Also edits existing reports. |
| `board-story` | Builds a board story one page at a time: agrees the pages and their order, shows each report page, asks what drove the key numbers, writes the headline and commentary, and saves the page. Where the report image can't be shown, as in Claude Code, it shows the page's key figures as a small table instead. Also rolls a story forward to a new period and exports it for PowerPoint, PDF or other formats. |

Claude picks the right skill from your request. You can also name it, for example "use the finstory board-story skill".

## Example prompts

1. "How did the last closed month go against budget? Give me the three biggest variances in operating income and what drove each one."
2. "Which cost lines are over budget year to date, and by how much?"
3. "Build a report with revenue, gross margin and EBITDA by month for this year, actual against budget. Show me a preview before you publish it."
4. "Draft the board story for the last closed month: a P&L page, a revenue page and a cash page, each with a headline and two lines of commentary. Ask me when the data can't explain a movement."
5. "Open last month's board story, move it to the latest closed month and update the headlines where the story changed."

## Data and privacy

- **The plugin itself contains only instructions.** The skills are text files. The plugin runs no code, stores nothing and contacts nothing on its own.
- **The bundled connector** sends tool calls only to `https://mcp.finstory.ai`, on your behalf, after you sign in with your finstory login. Every call runs within your finstory role: it reads only what your role can read, and it creates or changes reports and stories only where your role allows.
- **What the connector can change:** it creates and edits reports and stories in your finstory workspace. New reports stay hidden until they are published. It has no tool that deletes a whole report or story; it can remove single items (a story page, a tag, a report component or list item), and the skills tell Claude to do that only when you ask.
- **Approvals:** every connector tool is marked as read-only, additive, or able to overwrite, so Claude can ask for your approval before a change runs, depending on your Claude settings.
- **Privacy policy:** https://finstory.ai/privacy#finstory-in-claude
- **Documentation:** https://finstory.ai/docs/claude

## Support and security

- **Support:** support@finstory.ai
- **Security issues:** see [SECURITY.md](SECURITY.md) (security@finstory.ai, https://finstory.ai/security#report).

## License

Apache License 2.0. See [LICENSE](LICENSE). Copyright Finstory, Inc.

finstory is a product of Finstory, Inc.
