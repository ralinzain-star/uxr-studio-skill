---
name: survey-analysis
description: >
  This skill should be used when the user asks to "analyze the survey results", "what did the
  survey say", "cut the survey by segment", or has raw questionnaire data that needs cleaning,
  weighting, and interpretation. Produces a cleaned dataset note plus a findings write-up with
  distributions, defensible cuts, and stated uncertainty.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# Survey Analysis

Lead with the distribution, not the mean. A bimodal result averaged into a midpoint is a lie
that reads as a finding. Report a difference between groups only when it survives a sample-size
and multiple-comparison sanity check; everything else is a hypothesis, labelled as one.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A survey has closed and someone wants numbers, cuts, and a recommendation.
- A stakeholder is quoting a survey average whose underlying shape was never checked.
- Open-text responses need coding into countable themes.
- Do not use this when the data is interview transcripts or session notes, even if a rating
  scale was used. Use `thematic-coding` or `affinity-synthesis` instead.

## Gather first

1. The original estimand and the pre-registered segment cuts from the sampling plan.
2. The frame, the invited n, the completed n, and the completion rate.
3. Whether population proportions for the frame are known, which decides whether weighting is
   possible at all.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Clean before you look at any result.** Looking first contaminates the cleaning rules.
   **Hard stop: if the raw response data is not in hand, or the segments the sampling plan
   declared are not known, do not continue.** Ask for both with AskUserQuestion and wait. Never
   infer a distribution, never invent a segment to cut by, never write "TBD", and never proceed
   assuming the export will arrive later. Cuts chosen after the numbers are seen are fishing, and
   an analysis written around figures nobody supplied is the one failure this skill cannot
   recover from. The only exception is an explicitly unattended run, in which case emit the
   verdict `Blocked: no raw response data or no pre-declared segments` and nothing else.

   Drop, in this order, and log the count removed at each step:
   - Speeders: completion time under one third of the median.
   - Straightliners: identical response on every item of a multi-item scale block of 5 or more.
   - Contradictory pairs: at least one designed pair per survey, for example claiming no scans in
     30 days then rating a recent scan. Drop the respondent, not the item.
   - Duplicates on respondent id where `~~survey tool` exposes it.
   If cleaning removes more than 15% of completes, stop and report a data-quality problem rather
   than an analysis.
2. **State the denominator on every number.** Percentages of completes, of a screened subgroup,
   and of item-level responders are three different numbers. Pick one and say which.
3. **Report the full frequency distribution first**, then central tendency. Inspect every scale
   item for bimodality and endpoint piling. If a distribution is bimodal, delete the mean from
   the write-up and name the two modes as two populations.
4. **Weight only when the frame's true proportions are known**, for example plan state or tenure
   from `~~data warehouse`. Never weight to guessed shares. Geography is the trap: raw traffic
   over-represents regions contributing negligible revenue, so a monetization question is either
   weighted to the paying base or reported unweighted and scoped to the population actually
   sampled. Show weighted and unweighted side by side, and flag any weight above 3.
5. **Cut only the segments named in the sampling plan.** Fishing across every demographic
   guarantees a false positive. Require at least n=100 per cell before reading a difference in
   proportions, and at n=100 treat anything under roughly 10 points as noise. If more than 3
   comparisons are made, tighten the threshold: divide 0.05 by the number of comparisons, or
   report the cut as exploratory and say it needs replication.
6. **Code open text against the closed item it explains.** Build the codebook from the first 50
   responses, then apply it to all. Above 200 responses, code a random 150 and call the
   frequencies approximate. Report each code as a count out of coded responses, never as a
   percentage of the full sample, and carry two verbatims per code.
7. **Write uncertainty in a sentence a stakeholder will read.** Not "p = 0.04, n = 412". Use the
   shape: "About 3 in 10 trial users said X (28%, and the true figure is very likely between 24%
   and 32%). That range is wide enough that X and Y cannot be ranked against each other."
8. **Separate the numbers that answer the estimand from everything else** and mark the rest as
   context. A survey producing 30 findings has produced none.

## Output

- **Headline** — one sentence answering the estimand, with its interval.
- **Data quality** — invited, completed, completion rate, removals by rule, final n, and the
  geography and plan-state composition of that final sample.
- **Distributions** — one frequency table per estimand item, with the shape described in words.
- **Pre-registered cuts** — each cut with cell n, difference, interval, and a verdict of holds,
  does not hold, or underpowered.
- **Exploratory observations** — clearly labelled, each with "needs replication".
- **Open-text codes** — code, count, two verbatims.
- **What this cannot tell you** — the causal and predictive questions left unanswered.

## Quality bar

- Cleaning rules were written and applied before any result was inspected, and removals are
  logged by rule.
- Every reported percentage carries a denominator and an interval or explicit cell n.
- No mean is reported for a bimodal or endpoint-piled distribution.
- Every segment cut appears in the sampling plan, or is labelled exploratory.
- Weighting is either absent or justified by known frame proportions, shown both ways.
- The write-up states at least one thing the survey cannot answer.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `insight-writer` next unless the user redirects, turning the headline and each
  defensible cut into a claim.
- Invoke `so-what-checker` now on those claims. It is a mandatory gate: no survey number reaches
  anyone before it has run.
- Invoke `pyramid-report` once claims survive.
- If the numbers show a shape but no cause, invoke `interview-guide-builder` to commission the
  follow-up.
- If a result contradicts `~~product analytics` or a qualitative round, invoke `triangulation`
  first.
- Stop here if cleaning removed more than 15% of completes. Report the data-quality problem.
