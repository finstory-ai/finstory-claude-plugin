# Building a finstory story page by page

Every tool named here is a finstory connector tool.

Contents

- [The page loop](#the-page-loop)
- [Asking the page's questions](#asking-the-pages-questions)
- [Writing the page](#writing-the-page)
- [Layout](#layout)
- [Notices in the pack](#notices-in-the-pack)
- [Editing a saved story](#editing-a-saved-story)
- [Rolling a story forward](#rolling-a-story-forward)

## The page loop

One page at a time, in the order agreed with the user:

1. **Show the page.** Call the finstory connector's `story_get_display_pack` tool with the story's `documentId`, the page's `report_id` from `report_list`, the topic, and a `pov` if the page shows one part of the business. You receive a summary of its figures (headline cards, the largest bridge movements, the top and bottom table rows) exactly as the report shows them. On a host that supports MCP Apps the user also sees the real report as an image; the page notes say whether an image came with this result.
2. **Read the page.** A few sentences of prose: what stands out and what it implies, read through the topic's lens ([topic-lenses.md](topic-lenses.md)). Refer to figures by name and value.
   - If the user is shown the image, write around the report they are looking at rather than redrawing its tables, cards or charts.
   - If no image came with the result, tell the user why in one line as "No image" under [Notices in the pack](#notices-in-the-pack) says (a reason that is about the connection, only the first time). Unless the story-page app has reported what it shows, show the headline figures and the rows your questions will cite as one small table on every such page, quoted exactly and labelled as the page's summary, and write the read from those figures. Don't describe a picture or say the page is on screen.
3. **Ask the page's questions** (next section). Your reply ends with them.
4. **Save the page** once the user has answered: `story_add_page` with the headline, commentary and storyline fields, and the same `pov` you passed to the pack. It returns a `pageId`.
5. **Add the page content**: tags, text blocks and notes pinned to table cells or chart points, in one `story_add_page_content` call. Nothing is saved if any item is refused, so fix the item it names and send the call again.
6. **Confirm before the next page.** Tell the user the page is saved, in a line or two, and ask whether to go on to the next one.

If a question genuinely needs a number the summary left out, `story_get_display_pack_detail` returns more rows and chart points of the tables and charts in the summary. Each table's note says which rows it keeps. A table wider than eight columns is also cut to eight, and its note may not say so, so a month or column missing from it is not proof the report lacks it: ask the user for the figure rather than saying it isn't there.

## Asking the page's questions

- Ask two to four questions, as one short set (use a choice widget if the host has one; otherwise a short numbered list). Scale the count to how much the page actually contains.
- Each question names a specific number from the pack and asks what caused it. Take them from the largest card movements, the biggest bridge legs, and the top and bottom rows of ranked tables. For example: "Freight is 140k over budget in June. Is that volume, rates or a one-off?"
- A question the user could answer without looking at the data isn't worth asking.
- Don't ask about formatting, layout, tone, or anything already known (the number of pages, the period, the story title). The questions are there to understand the numbers, not to collect styling choices.
- When the user can't explain a movement, write what the data shows and say that the cause is still open, rather than guessing one.

## Writing the page

- **Headline (title):** the finding in a few words, with the decision word in the second part of the title. The `story_add_page` tool describes the title and subtitle markup it expects.
- **Commentary (subtitle):** two or three short paragraphs, or the length the user asked for. Lead with the conclusion, back it with one or two figures from the pack, and fold in what the user told you.
- **Storyline fields:** a short label ("Revenue") and a one-line takeaway for the sidebar.
- **Notes on the report:** pin a note to the cell or chart point it explains, with a short badge. Keep one badge colour per meaning across the story.
- Quote every figure exactly as the pack gives it. Don't recompute, rescale or combine figures, and don't state a number that wasn't in the pack or the user's answers.

## Layout

Placement follows the pack's Layout line; there's no need to ask the user about it.

- Commentary goes beside the report for a single narrow table, and above it for wide tables, multi-column reports and pages without a wide table.
- When the commentary sits beside the report, notes on the report go outside it.

## Notices in the pack

- **A cut the report doesn't allow:** if the pack says the page asks for a scope the report ignores, clear it with `story_update_page` rather than writing about a slice the page isn't showing.
- **Components that couldn't be read:** if the pack says some components could not be read, their figures are missing from the summary, so don't describe the page as complete; tell the user which part is missing.
- **Beyond the summary's limit:** if the pack lists tables, charts or headline figures beyond its limit, they are on the report page (and in its image, where one is shown) but their figures are not in the summary or in `story_get_display_pack_detail`; quote no figures from them unless the user gives them.
- **A chart that failed to render:** if the pack says a chart didn't render in the image, tell the user that chart is missing from the image and don't describe what it would show. The figures in the summary are still exact.
- **No image:** the page notes say no report image comes with the result. The summary figures are exact either way, and the page can still be written. The first two cases below are about the connection, not the page, so give their one-line explanation only the first time in the conversation; the small table from loop step 2 goes on every page where the user may not see the figures.
  - *No further notice* (as in Claude Code): the user sees nothing of the page unless you show it. Tell them the report image isn't shown in this app and show the figures as in loop step 2.
  - *A notice that no image was rendered for this connection* (the connection didn't declare MCP Apps, as in Claude Code) *and that the story-page app loads it itself:* where the host shows that app, the user may be looking at the report image while you write, so don't say it is missing and don't describe a picture. Say in one line that the page's key figures follow in case the report image doesn't appear here, and show them as in loop step 2. Where the app reports what it is showing (the report image, or the page's figures as tables), write around that from then on and leave out the table.
  - *A notice that the image was too large to send or could not be rendered:* say in one line that the report image is missing, and don't describe a picture. If the notice says the story-page app shows the page's figures as tables instead, write around those; otherwise show the figures as in loop step 2.

## Editing a saved story

1. Find the story with `story_list` and read it with `story_get` (the default structure view shows what is on each page).
2. Change only what the user asks for:
   - a page's headline, commentary, storyline fields or scope: `story_update_page`;
   - the report a page shows: `story_update_page` with the new `report_id`, which keeps the page's tags and notes (removing the page and adding a new one would lose them);
   - the order of pages, or the story's year and period: `story_update`;
   - removing a page or a tag: `story_remove_page` or `story_remove_chip`, only when the user asks.
3. Re-show a changed page with `story_get_display_pack` and its `pageId`, and rewrite its text from the new figures.

## Rolling a story forward

For "move last month's story to the latest closed month":

1. Open the story with `story_get` and confirm the new period with the user (from the year and period members in the context; for "the latest closed month", propose it as in the skill's Start step 3: the latest month with actuals, never the current calendar month).
2. Move the whole story with `story_update`, passing every scope setting to keep, including year and period. Pages with their own scope keep it.
3. Go through the pages one at a time: re-show each with `story_get_display_pack` and its `pageId`, compare the new figures with the existing headline and commentary, and propose an update only where the story has changed or the wording names the old period. Confirm each change with the user before saving it with `story_update_page`.
4. On the same page, check its tags, text blocks and notes (`story_get` lists them per page) for wording about the old period or figures that have since moved. With the user's agreement, replace a stale tag by removing it with `story_remove_chip` (highest `chipIndex` first, so the others keep their positions) and adding the new one with `story_add_page_content`. Text blocks and notes can only be added from here, not changed or removed, so list the stale ones with a suggested rewording for the user to edit in finstory rather than adding a second one beside them.
5. If the pack's POV line doesn't show the new period, find out why before you tell the user:
   - `story_get` shows the page's own scope setting a year or period: that overrides the story. Offer to update it with `story_update_page`, whose `pov` replaces the page's scope outright, so pass the parts to keep, or `{}` to clear it.
   - The story's period is `"@default"`: each page follows its own report's default period. Offer to set the new year and period on the story with `story_update`.
   - Otherwise the page's report fixes its own period and doesn't move with the story; its figures change only where new data was loaded. Tell the user, and word its headline and commentary for the period it actually shows.
6. Finish on the story link if `story_get` returned a `story_url`; otherwise name the story and the pages that changed. Don't build a link from its id.
