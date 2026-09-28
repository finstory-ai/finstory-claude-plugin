# Exporting a finstory story

A saved story lives in finstory and is shared through its story link. When the user wants the story in another format (PowerPoint, PDF, a Word document, a spreadsheet, or their own board template), export it and hand the result to whatever tool the user chooses for that format.

## Steps

1. Call the finstory connector's `story_get` tool with the story's `documentId` and `view: "export"`. Add a `pageId` for a single page; leave it out for every page in story order.
2. The export contains each page's copy (headline, commentary, storyline fields), the live tables in full with their display strings, and the notes with the cells they are pinned to. Charts and KPI tiles are not included.
3. Build the requested format from the export with the tool or skill the user prefers, or the one best suited to the format they asked for.

## Keep the figures as exported

- Use the display strings exactly as exported. Don't recompute, rescale or round figures differently.
- Keep each note next to the row or cell it is pinned to.
- Tell the user that charts and KPI tiles aren't part of the export. If they want a chart in the new format, draw it from the exported table figures without changing them, and say that it was redrawn.

## After the export

Give the user the file or result in the format they asked for, and the story link, so the finstory version stays the reference.
