# Rebuilding a report from a picture

When the user shares a picture of a report (a screenshot, a slide, a PDF page) and wants it in finstory, the picture is the design: follow it, and ask only what it cannot show, such as which model to use when the company has several, or whether to publish.

## 1. Write down everything it shows

Before any build call, write a spec from the picture:

- The header: each selector and the value it shows.
- Every component, with its place in the layout (row, column, roughly how wide), what kind it is (table, chart, tile, side panel, scale strip), its title as printed, and the figures, labels, chips, legends and colours it shows.
- For a table: every row, top to bottom, including label-only rows and empty gaps, with how each is styled (bold, italic, indented, a rule above, a chevron that expands it) and its figures; and every column, including a column of bars.
- The number format: currency, scale, decimals, and how percentages print.

Read every figure exactly: the build is checked against them.

## 2. Build it part by part

Split the build into parts: the report and its header first, then one part per component, or per group of components that share lists. If the host can hand work to sub-agents, give each part to one, one at a time because they all edit the same report. Each task names the report id, the part's place in the grid, everything the spec says about it, the members and lists to use, and what to check before it finishes. Otherwise build the parts yourself in the same order. Either way, each part follows the Build steps in this skill.

## 3. Compare the preview with the picture

When every part is built, render the report with the finstory connector's `report_render_preview` tool and compare it with the picture component by component: titles, figures, chips, chart shapes, colours, order and layout. Check exact figures with `report_preview_values`, and read `render_status` as well as the image. If the host shows you no image, compare the outline and the figures with the spec instead.

Write down every difference, fix it, and render again. Stop when the preview matches the picture, or after three rounds of fixes, and tell the user what still differs and why, such as a setting the tools don't offer.

Negative numbers in brackets or with a minus sign follow each viewer's own display setting, so that difference is not the report's to fix.
