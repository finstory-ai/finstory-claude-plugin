# Writing the story

The page is read by a board member scrolling on a laptop or phone. They read the headline, glance at the chart, and
read a step only when the chart makes them curious. Every line has to carry a finding.

Contents: 1 Headlines · 2 Steps · 3 Numbers in text · 4 Claims you can defend · 5 Stale commentary · 6 Hero, appendix,
notes · 7 Examples

## 1 · Headlines

- `title` is two short sentences split by `|`. The first states the finding, the second the twist or the reason; the
  engine shows the second in green. Each half under about 26 characters: a longer half wraps to two lines on desktop,
  and four lines of display type is heavy. The hero title is set larger: halves of about 14 characters ("Margin up
  4.4 points. | Staff did half." runs to four lines there, which is the most a hero should take).
- Say what happened, not the topic. "Gross margin" is a label; "Margin up 4.4 points. | Labour did half of it." is a
  headline.
- Under the lens, write one headline per comparison: `{"$lens": {"bud": "Sales beat plan. | …", "ly": "Sales up a quarter. | …"}}`. A
  budget headline that says "up on last year" is wrong when the reader switches.
- `eyebrow` is the period and topic, "May 2026 · Gross margin", under about 40 characters. The engine adds the
  chapter number and the `section`; don't repeat them.
- `rail` is one or two words: "EBITDA", "Channels", "Cash".

## 2 · Steps

- `when`: 1–3 words naming what the step looks at ("Revenue", "Jun–Sep", "Online").
- `title`: the number plus the finding, under about 45 characters: "Online − Direct missed by $661K".
- `body`: one or two sentences, under about 220 characters: why, what it means, or what to watch.
- Order steps from the whole to the parts, or along the bridge: start, the biggest leg, the next, the end.
- Sources of steps, in order of preference: the page's annotations (pins on cells), the paragraphs of its subtitle,
  then the chart itself (its biggest mover, its total, its outlier).
- Three to five steps per chapter. A step with nothing new to show belongs in the previous step's body.

## 3 · Numbers in text

You never type a number: every figure in text is a placeholder that finstory fills in and sources
(references/reference-format.md). The examples below show what the placeholders read as.
- Money: `{{x|money}}` gives $12.7M, $354K, $950; `{{x|money.2}}` gives $1.32M.
- Changes: `{{v|money +}}` → "+$354K", `{{p|pct +}}` → "+5.0%", `{{d|pts +}}` → "+1.4 pts", `{{r|x}}` → "2.5x".
- Direction in words: `{{v|updown}}`, `{{v|overunder}}`, `{{v|betterworse}}` (and `betterworse-` for costs), then
  the amount with `abs`: "Store costs came in {{v|money abs}} {{v|overunder}} budget".
- Negatives print with the typographic minus (−), or in parentheses where the workspace uses them.
- Name every comparison: "vs budget", "on last May", "vs the June reforecast", "vs the 2025 run rate". A column
  labelled "LY" may hold something else; check one figure before trusting a label (finstory reports sometimes carry
  the full prior year in an "LY YTD" column).
- A ratio the board has seen (a margin, a share) comes from the report's own ratio cell, not a recalculation.
- KPI subs carry one comparison in 22 characters or fewer ("vs {{revLy|money}} in 2024"); the second goes in a step.
- A figure in the text and the same figure in its chart come from one figure name, so they always agree.

## 4 · Claims you can defend

- Check every pattern claim against the series: "every month since March", "two months running", "no channel
  missed". If one month breaks it, rewrite.
- Don't invent causes. The data shows what moved; the user's answers say why. Mark your own reading as one, in the
  text ("most likely the timing of rebates; worth confirming with the controller") and as the step's
  `basis.assumption`; a cause the user gave rests on their answer (`basis.answers`).
- A % on a tiny or opposite-sign base is `n.m.`; say "turned positive" or give the amounts.
- Cost lines: say "4.7% over budget" or "$41K under", in words, never a bare "+4.7%" beside a red bar.
- Unbudgeted lines (interest, tax, one-offs) are findings in their own right: say the budget had none.
- When the data contradicts the story's text, the data wins (section 5).

## 5 · Stale commentary

Story text is written once; reports are live. Before writing, compare the two or three figures in each page's
subtitle with the display pack (the structure's `updatedat` dates the text).
- Build on today's figures: a chart can only show current data, and text that contradicts its chart is worse than
  either.
- Keep the story's page order, its questions and every claim that still holds; rewrite the rest from the data.
- An old figure from the story's text cannot be typed into the scroll story. Say in words what changed ("the text
  was written before the September close") and say so in your reply.

## 6 · Hero, appendix, notes

- Hero `title`: the whole story in two sentences. `lead`: the headline figures. `strong`: the watch-item. `meta`:
  what the page covers ("13 board pages · 11 chapters", "Figures in $000 unless noted").
- Appendix: the tables a reader would want to look up (the P&L for the period with the lens columns, the main
  breakdown, cash flow), each with a `note` on units and sign convention.
- Sources need no notes: finstory lists them under every chapter. `notes` are kept with the story but not shown.

The examples below show stories as readers see them; you write each figure as a placeholder.

## 7 · Examples

| weak | strong | why |
|---|---|---|
| "Revenue overview" | "Sales beat plan. \| Every channel but one." | a finding, not a topic |
| "EBITDA was $1,324,144 in May" | "EBITDA $1.32M, +$354K vs budget" | rounded, compared |
| "Costs +4.7%" (beside a red bar) | "Store costs 4.7% over budget" | the sign can't be misread |
| "Margin improved due to pricing" | "Margin up 1.4 pts; the story credits pricing" | the cause is attributed |
| "Net profit missed again" | "Net profit missed: interest had no budget" | the reason, checked |
| "Growth of 454.9%" | "Marketplace turned a loss into $32K profit" | n.m. replaced with amounts |
| "Strong performance across the board" | "Six of nine months beat budget" | countable, checkable |
