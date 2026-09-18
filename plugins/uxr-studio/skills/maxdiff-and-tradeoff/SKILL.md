---
name: maxdiff-and-tradeoff
description: >
  This skill should be used when the user asks to "prioritize these features", "run a MaxDiff",
  "which message resonates most", or needs to rank a set of items by forcing respondents to
  choose rather than rate. Produces a best-worst scaling design, fielding parameters, and a
  utility ranking with explicit limits on what it can conclude.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# MaxDiff And Tradeoff

Rating scales produce ties, because on a 1 to 5 importance scale everything is a 4. Force a
choice instead. Best-worst scaling is the default here: it is cheap to field, robust, and easy
to explain to a room. Reserve full conjoint for genuine multi-attribute tradeoffs where the
combination matters, which in practice means pricing and packaging.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A roadmap has 12 to 30 candidate items and stakeholder opinion is deadlocked.
- Messaging or value-proposition options need ranking before a campaign or a landing test.
- A prior importance-rating survey came back with everything rated important.
- Do not use this when the question is what to build that nobody has thought of yet, or why an
  item matters. Use `interview-guide-builder` or `opportunity-backlog` first to generate the list.

## Gather first

1. The decision this ranking feeds and who will act on the order.
2. The candidate item list, its origin, and whether anything is already committed.
3. The population and whether segment-level reads are required, which drives sample size.
4. Whether price or plan composition is part of the question, which routes to conjoint instead.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Pick the instrument.** Best-worst scaling for one-dimensional prioritization: which of these
   items matters most and least. Choice-based conjoint only when the respondent must trade
   attributes against each other at once, such as plan interval against feature set against price.
   Conjoint costs several times more to design, field and explain. Do not reach for it to rank a
   feature list.
2. **Write items at parallel scope.** Every item should sit at the same altitude. Mixing a small
   speed improvement with an entirely new product area guarantees the big one wins and tells you
   nothing. Build the example from the features named in the product context: keep each named
   feature at feature level, and never list a whole feature alongside a single control inside
   one of them.
3. **Ban compound items.** One benefit per item. "Faster and more accurate scanning" is two items
   and an unreadable result. Split it or drop half.
4. **Use user language, not internal names.** Describe the outcome the user gets, in words from
   `~~research repository` transcripts or `~~support desk` tickets. Avoid the product's own
   framing, which primes agreement. Keep every item to 12 words or fewer and check each one
   against the participant-facing language rules in the product context.
5. **Set the design parameters.** 12 to 30 items total. Four to five items per screen. Each item
   appears three or more times per respondent, and every respondent sees 10 to 15 screens. Use a
   balanced incomplete block design so each item appears equally often and co-appears with every
   other roughly equally. More than 30 items means splitting the list into two studies, not
   lengthening the survey.
6. **Size the sample for the read you promised.** 300 respondents for a whole-population ranking.
   200 to 250 per segment if you promised segment-level reads, which is the cost people forget
   when they casually ask for a cut by segment. Recruit from the population the decision applies
   to, not from whoever is easiest to reach. Use the sampling trap named in the product context as
   the worked example: any monetization-adjacent prioritization must be fielded to the population
   that can actually pay, because a convenience sample is weighted toward the group that trap
   describes.
7. **Add two validity items.** One trap item that should rank last if respondents are reading, and
   one item already known to be top-ranked as an anchor. Drop respondents who fail the trap and
   report how many.
8. **Read the utilities as relative, never absolute.** Report rescaled utility scores summing to
   100 across items, with confidence intervals. A score of 12 means twelve percent of preference
   share among these items, not that 12% of users want it. Report the gaps, not the ranks: items
   whose intervals overlap are tied, and presenting them as first and second place manufactures a
   decision the data did not make.
9. **State the discovery limit in the deliverable itself.** This measures stated preference among
   the options you chose. It cannot surface an option you left out, and it cannot tell you why an
   item won. Both of those need qualitative work, and the top two items should be routed there.

## Output

- **Decision and item list** — each item, its source, and the user-language wording as fielded.
- **Instrument choice** — best-worst or conjoint, with the one-line reason.
- **Design spec** — item count, items per screen, screens per respondent, appearances per item,
  design type.
- **Sample plan** — n total, n per segment, source population, geography, trap-item handling.
- **Results** — utility table with rescaled scores and confidence intervals, tie groups marked.
- **Segment comparison** — only if powered for it, otherwise say it was not.
- **What this cannot tell you** — the discovery limit and the absence of causal explanation.
- **Follow-up study** — the qualitative question for the top two items.

## Quality bar

- All items sit at the same scope and none is compound.
- Item wording uses user language, avoids product framing, and passes the participant-facing
  language rules in the product context.
- Each item appears at least three times per respondent under a balanced design.
- Sample size matches the segment-level reads that were promised, or the promise was withdrawn.
- Results present tie groups and intervals, not a clean 1 to N list.
- The deliverable states that unlisted options were untestable.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now unless it has already cleared this study. It is a
  mandatory gate: nothing fields before the harm review.
- Invoke `survey-builder` next unless the user redirects, then `survey-analysis` when the data
  lands.
- If price or plan composition entered the item list, stop and invoke `pricing-sensitivity`
  instead.
- If the top two items need a why, invoke `interview-guide-builder`, and send the ranking to
  `opportunity-backlog`.
