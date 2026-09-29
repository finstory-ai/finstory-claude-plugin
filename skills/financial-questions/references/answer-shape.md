# Answer shape for a finstory question

A finstory answer has four parts, in this order:

1. **Headline**: one sentence, under 25 words, with the finding and its size.
2. **The visual**: the tile row, table or chart the answer turns on, together in one place, with no paragraphs or questions inside it. See [visuals.md](visuals.md).
3. **The reading**: what the visual can't say.
4. **Where next?**: two to four lettered options for the next cut.

A simple lookup ("What was revenue in May?") needs only a sentence or two: the figure, its comparison, and at most one line of context.

## The reading

The reading is part of every answer that has a visual. It sits outside the visual, in plain sentences: no headings, no bullets, no second table.

- One short paragraph per driver, three drivers at most, two to four sentences each, about 200 words in all.
- Every sentence adds a cause, a comparison with an earlier period, or a classification (one-off, timing, structural). Each one rests on:
  - a figure on screen;
  - a figure from another period, quoted with its period and value, from a query; or
  - a fact from the company profile applied to such a figure.
- Don't restate the table.
- When the data doesn't show the cause, write "cause not visible at this level" and make finding it a "Where next?" option.
- End each paragraph on its verdict.
- Data caveats go last, one line each.

## Where next?

The last thing in the reply is the line "Where next?" followed by two to four lettered options, A) to D). Each option names the exact cut it would run and what it would settle, for example "Marketing costs for June, drilled to individual accounts, to find the one-off campaign charge."

The user answers with a letter or in their own words; run that cut. Plain lettered lines work in every host. If the host offers a choice widget, the same lettered options can go there instead. Close on the options rather than an open "let me know what else you need".

## Worked example

Synthetic figures, in full units with two decimals as an ad-hoc result's `formatting` gives them. No currency is printed because the question's scope sets none (see [reading-results.md](reading-results.md)).

Question: "How did June go against budget on operating income?"

> Operating income was 420,000.00 below budget in June (1,180,000.00 against 1,600,000.00), with marketing and freight explaining most of the gap.
>
> *[One visual: a diverging bar of the six largest variances in profit impact, marketing and freight at the left, a small favourable revenue bar at the right.]*
>
> Marketing ran 250,000.00 over budget, almost all in paid campaigns. May was on budget at 310,000.00, so this looks like a one-off launch spend rather than a new run-rate; the account detail would confirm it.
>
> Freight was 140,000.00 over budget as volumes rose 6% while the rate per shipment held. That makes it volume-driven, and it moves with the revenue beat rather than against it.
>
> Revenue was 50,000.00 ahead, not enough to offset either cost line. Which regions carried it: cause not visible at this level.
>
> Caveat: the result flagged one entity's June actuals as provisional.
>
> Where next?
> A) Marketing for June, drilled to individual accounts, to confirm the one-off campaign charge.
> B) Freight by region against budget, year to date, to see whether the overrun is one region or all.
> C) Revenue by region for June against budget, to find where the 50,000.00 beat came from.
