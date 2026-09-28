---
name: research-roi
description: >
  This skill should be used when the user asks to "calculate research ROI", "what did that study
  save us", "prove research value", "quantify our research impact", or needs a defensible number
  for what a study or a research portfolio returned. Produces a ranged, counterfactual-backed
  value claim in cost avoided and decisions changed, net of research cost.
metadata:
  version: "0.1.0"
  stage: "impact"
---

# Research ROI

Claim cost avoided and decisions changed. Never claim revenue. Revenue attribution for research
is indefensible, because the shipped change, the pricing move, the season and the market all
touch the same number, and the claim gets torn apart the one time it matters. Claim the build
cost of the thing you stopped, the experiment you did not need to run, and the rework you
prevented. Those survive audit.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A finished study or a quarter of studies needs a value number for planning or review.
- A stakeholder asks what a specific study got them.
- A value number is needed as an input to a funding ask. Produce it here, then hand it to
  `business-case-builder`, which makes the argument.
- Do not use this when the question is which metric a finding moves. Use `decision-metrics-mapper`.

## Gather first

1. The studies in scope, with dates, cost, and the decision each fed. Pull from `impact-tracker`
   rather than reconstructing from memory.
2. For each study, what the team was going to do before the research, and the evidence that path
   was real: a roadmap slot, a ticket, a staffed team.
3. The fully loaded cost of one team-week, from whoever owns the budget.
4. Who will read the number and what they will try to reject.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Sort every claim into one of four categories. Anything that fits none of them is dropped,
   not softened.**
   - **Build cost avoided.** The team was going to build something and did not. Claim the
     scoped build cost in team-weeks.
   - **Experiment cost avoided.** The research answered a question an A/B test would have
     answered. Claim the test's engineering and opportunity cost, plus the time the test would
     have held the surface. Check the feedback-loop length in the product context: a test on a
     slow loop costs far more calendar time than teams assume.
   - **Rework prevented.** The research changed a spec before build, not after. Claim the delta
     between changing it then and changing it post-launch, at the organization's own observed
     rework multiple, not a published industry one.
   - **Decision time returned.** A stalled debate closed. Claim the team-weeks the argument was
     consuming, measured from the calendar, not estimated.
2. **Establish the counterfactual with three tests. Two must pass or the claim is downgraded to
   a mention with no number.** The intended path was written down before the readout. The named
   decider states they would have gone the other way. The alternative path was actually
   resourced. A counterfactual invented after the decision is worth nothing and reads that way.
   **Hard stop: if the counterfactual for a claim is not known — what the team was actually going
   to do, evidenced before the readout — do not continue to a number for that claim.** Ask for it
   with AskUserQuestion and wait. Never infer the alternative path, never write "TBD", and never
   proceed assuming the evidence will turn up later. A value claim without a counterfactual is
   the first line finance cuts. The only exception is an explicitly unattended run, in which case
   emit the verdict `Blocked: no evidenced counterfactual` and nothing else.
3. **Convert everything to team-weeks first, then to money once, at the end.** Mixing units
   mid-argument is how the arithmetic gets challenged instead of the conclusion.
4. **Claim a range, never a point.** Low bound is the scope that was definitely committed. High
   bound is the full scope as scoped. Lead with the low number and let the high one sit behind
   it. A point estimate invites a fight about the point.
5. **Subtract research cost and report net.** Include researcher time, incentives, tooling, and
   stakeholder hours in sessions. A gross number looks evasive.
6. **Apply the finance test to every line.** If a skeptical finance partner would reject it, cut
   it. The four standard rejections: revenue attributed to research, a metric improvement with
   other plausible causes, a decision that would have happened anyway, and the same avoided cost
   counted in two studies.
7. **Include the zeros.** Studies with no recorded decision enter the portfolio at zero. A
   portfolio that is all wins is not believed, and the zeros are what make the rest credible.

## Output

- **Headline** — net value as a range, over a named period, and the count of decisions changed.
- **Claims table** — one row per study: category, low, high, counterfactual tests passed (2 of 3
  or 3 of 3), and the evidence for the intended path.
- **Excluded** — claims considered and dropped, with the rejection reason. This section is what
  makes the rest survive review.
- **Research cost** — the full line, itemized.
- **Method note** — team-week rate, rework multiple used, and its source.

## Quality bar

- No claim contains a revenue figure or a revenue-derived estimate.
- Every claim names its counterfactual evidence and how many of the three tests it passed.
- Every number is a range, and the low bound is the one in the headline.
- Studies with no recorded decision appear at zero, and the excluded section is non-empty.
- Research cost is subtracted, and the headline is net.
- Every avoided cost appears exactly once across the portfolio.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `impact-tracker` now to record each claim, its counterfactual and the tests it passed,
  so next period's number is read rather than reconstructed.
- If the number supports a headcount, tooling or budget ask, invoke `business-case-builder` next.
- If a claim failed two of the three counterfactual tests, invoke `decision-metrics-mapper`
  instead, to tie it to a number the company already tracks before it is claimed again.
- Stop here if the portfolio is being reported for information only and no ask follows.
