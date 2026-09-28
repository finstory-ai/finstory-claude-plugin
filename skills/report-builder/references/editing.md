# Editing an existing finstory report

Every tool named here is a finstory connector tool.

## Start from the outline

1. Call the finstory connector's `report_get_outline` tool with the report id (`report_list` finds it). It returns the current structure and a version; pass that version as `if_version` on the write that follows.
2. Say back to the user, in their terms, what you are about to change. A published report is shared with the company, so confirm before changing it.

## Which tool changes what

The part of the report you are changing decides the tool. Each one refuses the others' territory and names the right tool in its error.

| What changes | finstory connector tool |
| --- | --- |
| An item in a list (add, change, reorder) | `report_patch_list_items`; `report_remove_list_item` to drop one |
| Part of a component | `report_patch_component` |
| A whole component, rebuilt or replaced | `report_upsert_component` |
| Removing a component | `report_remove_component` |
| The scope, including the currency dimension | `report_set_pov` |
| Everything else: title, display currency symbol and scale, layout, labels | `report_patch` |

- For something unfamiliar, call `report_describe_schema` at that same address first. It returns only that one shape and what is set there now.
- `report_patch_component` and `report_patch_list_items` merge: send only what changes. `report_patch` applies its operations in order and rejects the whole call if any one fails.

## Removing components

`report_remove_component` tidies the emptied cell (and the row, if that was its last cell), so positions after it move; read what it reports, including `kept_intent_cells`. On a published report a removed component's id can't be reused, so notes pinned to it in board stories are lost for good. Mention that before removing a component from a published report, and afterwards pass on what `orphan_check` reports.

## Merging two reports

Call `report_get_outline` with `validate: "draft"` on both and keep the valid one as the base: an invalid report refuses every write.

## Finish

Run `report_get_outline` again, then `report_get_outline` with `validate: "final"` before re-publishing a report that is already published, and fix every issue it lists. Preview with `report_render_preview` when the change affects the layout, or `report_preview_values` when it affects figures, and show the user before publishing.

Taking a report off the menu (`report_finalize` with `publish: false`), retiring it from stories, or moving it to another group with `report_move` happens only when the user asks.
