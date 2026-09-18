---
name: survey-builder
description: >
  This skill should be used when the user asks to "write a survey", "draft a questionnaire",
  "add questions to the in-product survey", "poll our users", or needs to measure how
  widespread an attitude or behavior is across a population. Produces a field-ready
  questionnaire with screener, question order, scales, and a length ceiling.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# Survey Builder

A survey measures only what people can accurately report about themselves: prevalence, current
attitudes, and recent concrete behavior. It cannot measure causes, motives, or predictions. If a
question needs a "why", it is an interview question. Route it out rather than ask it badly.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Someone needs a number: how many users do X, hold attitude Y, and how that differs by segment.
- An existing survey produces flat or unusable data and needs rewriting.
- A qualitative finding needs sizing before it enters a roadmap argument.
- Do not use this when the question is "why did they do that" or "what would they do if" — use
  `interview-guide-builder`, or `concept-test-design` for reactions to something not yet built.

## Gather first

1. The decision the number informs, and the threshold that changes it.
2. The population and the frame it is sampled from (in-product, emailed list, panel).
3. Which segment cuts must be readable, and the target n for the smallest cell.
4. Fielding channel, and whether the respondent is mid-task.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the estimand before the questions.** One sentence: "Percentage of [population] who
   [did concrete thing] in [time window]." Every item serves that sentence or a named cut. Delete
   the rest. Curiosity is not a reason to keep a question.
2. **Screener and quota items at the top**, before any content. Screen on behavior, not
   self-labelled identity, and put quota variables (segment, plan state, geography) in the first
   three items so over-quota respondents terminate cheaply. Hand complex logic to
   `screener-builder`.
3. **Open with the easiest concrete behavior question.** Recent, countable, bounded window,
   answered in ranges rather than a free number. Build it from the core action described in the
   product context, in the shape "In the last 7 days, how many times did you <concrete action>?"
   It also yields a usable exposure variable.
4. **Then attitudes, then sensitive items, then demographics.** Attitudes asked after behavior
   are less rationalized, and sensitive items last means an early abandon still yields usable
   data. Treat employment status, income and rejection counts as sensitive and optional.
5. **One idea per question.** Scan every item for "and" and "or". "How satisfied are you with the
   speed and accuracy of your results?" is two questions and one uninterpretable number.
6. **Balanced scales with a labelled midpoint.** Five points by default, 7 when you expect
   ceiling effects. Label every point, not just the ends, with equal numbers of positive and
   negative options. A 4-point forced choice does not "make people decide", it manufactures
   signal.
7. **Drop NPS.** It collapses 11 points into three buckets, throws away resolution, and its
   benchmark value rarely survives contact with a real decision. Replace it with a 5-point
   satisfaction item plus one behavioral item: satisfaction with the last use of the feature
   under study, named from the product context, paired with "Have you used it in the last 14
   days?" The attitude and the reality check together are diagnosable. The score alone is not.
8. **Strip leading and loaded framing.** Keep product-owned vocabulary out of the stem. Take the
   banned words from the language and tone rules in the product context, and treat every feature
   name listed there as unusable inside a question. Spell out any industry acronym in full at
   first use. Rule out any item whose low score implies the respondent did something wrong.
9. **Cap length hard: 12 closed items, at most 2 open-text boxes, 5 minutes median.** For
   in-product intercepts, cap at 3 items. Every item past the ceiling costs completion on the
   items that matter. If the list runs longer, cut scope or split into two waves.
10. **Place each open-text right after the closed item it explains**, phrased "What made you
    choose that answer?" Open text parked at the end collects only complaints. Then pilot with 5
    people reading aloud, and rewrite any item they re-read or ask about.

## Output

Produce a single questionnaire document:

- **Estimand and decision**, one sentence each.
- **Frame, target n, smallest readable cell, sample geography.**
- **Section 1: Screener and quotas** — items, terminate logic, quota targets.
- **Section 2: Behavior**, then **Section 3: Attitudes**, then **Section 4: Sensitive and
  demographics** — every item written out in order with its full response options and scale
  labels, and a "prefer not to say" on each sensitive item.
- **Analysis note** — which items answer the estimand, which are cuts, which are diagnostics,
  and what was dropped and why.

## Quality bar

- Every item traces to the estimand or a named cut.
- No item contains "and", "or", or a causal or predictive stem ("why did you", "would you").
- Every scale is balanced, fully labelled, with a labelled midpoint, and no NPS item survives.
- Screener and quota items sit in the first three positions, sensitive items last.
- Closed items number 12 or fewer, open text 2 or fewer.
- No item implies the respondent is at fault, promises an outcome, or uses a feature name or a
  phrase the product context's language rules forbid.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `sampling-plan` next unless the user redirects, then `sample-size-advisor`, to confirm
  the frame and every quota cell.
- Invoke `research-ethics-review` now, before anything goes out. It is a mandatory gate:
  fielding a survey is fielding a study.
- Invoke `participant-comms` for invitation and reminder copy once the review clears.
- Invoke `survey-analysis` when the data returns.
- If the estimand will need a why as well as a size, invoke `interview-guide-builder` to design
  the paired qualitative strand.
