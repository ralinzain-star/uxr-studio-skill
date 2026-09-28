---
name: decision-metrics-mapper
description: >
  This skill should be used when the user asks "what metric does this move", "how do we measure
  the impact of this finding", "connect this insight to the business", or needs to tie a research
  finding to a number the company already tracks. Produces an explicit four-link causal chain
  with every link marked measured or assumed, plus the instrumentation plan and read date.
metadata:
  version: "0.1.0"
  stage: "impact"
---

# Decision Metrics Mapper

Research that cannot name the metric it moves gets cut in the first budget review. Map the chain
explicitly: finding, to behavior change, to product metric, to business metric. Mark every link
as measured or assumed. An honest chain with two assumed links beats a confident claim that
skips the middle, because the skipped middle is exactly where a skeptical reader will push.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A finding is written and needs a measurement story before the readout.
- A stakeholder asks how the team will know the change worked.
- A roadmap item sourced from research needs a success metric attached.
- Do not use this when the task is valuing work already done. Use `research-roi`.

## Gather first

1. The finding, stated as an observed behavior with its evidence and the segment it holds for.
2. The change being proposed, at the level of what a user will experience differently.
3. Which metrics already sit on a dashboard someone reviews, from `~~product analytics` and
   `~~data warehouse`.
4. The decision date and the date a read is expected.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Pick the business metric from the funnel and the decisions section of the product context,
   not from ambition.** It must be a metric the organization already watches. A metric invented
   for this study will not be reviewed and the chain dies with it.
2. **Build exactly four links, forward.**
   - **Finding.** The observed behavior, with the segment and the evidence base.
   - **Behavior change.** What a specific segment does differently after the change, phrased as
     a countable action, not a mental state. "Reaches the second step" is a link. "Understands
     the value" is not.
   - **Product metric.** The rate or count that aggregates that action, on an existing dashboard.
   - **Business metric.** The funnel-stage or revenue-adjacent number from the product context.
3. **Label every link measured, assumed, or unmeasurable.** Measured means there is data in hand
   for this population. Assumed means it is plausible and untested. Unmeasurable means no
   instrumentation exists and none is planned. State the label inline, never in a footnote.
   **Hard stop: if it is not known which links are actually measured for this population, do
   not continue the chain.** Ask with AskUserQuestion and wait. Never infer a label, never
   write "TBD", and never proceed assuming the data will turn up later. Marking an assumed link
   as measured is how a hypothesis gets presented as an impact claim, which is the one failure
   this skill exists to prevent. The only exception is an explicitly unattended run, in which
   case emit the verdict `Blocked: link status unknown` and nothing else.
4. **Apply the two-assumption rule.** A chain with more than two assumed links is a hypothesis,
   not an impact claim. Say that in the output and downgrade the language from "will move" to
   "is expected to move, conditional on".
5. **Write each assumed link as a testable statement with the cheapest test that would resolve
   it.** Name the test and its cost in days. Most assumed links resolve with an existing-data
   query, not a new study, so check the evidence sources in the product context first.
6. **Size the chain with reach times rate.** Eligible population, share exposed, expected change
   in the behavior rate, resulting change in the product metric. Show the arithmetic. State the
   magnitude that would be too small to detect, and if the expected effect is under it, say the
   chain is real but unobservable and stop there.
7. **Choose a leading indicator when the lagging metric is slower than the decision.** Check the
   feedback-loop length in the product context. If the business metric cannot resolve before the
   next decision on the same surface, promote the product metric to the primary read and hold
   the business metric as a confirmatory read with its own later date. The leading indicator must
   sit on the causal path, not merely correlate with it.
8. **Instrument before the change ships.** Name the event, the segment cut, the baseline window,
   the read date, and the owner. Confirm the baseline exists for the same segment definition. If
   instrumentation cannot land before ship, mark the chain unmeasurable and say so in the
   readout rather than promising a read nobody can produce.

## Output

- **Chain** — a four-row table: link, statement, status (measured / assumed / unmeasurable),
  evidence or test, cost of the test in days.
- **Verdict** — impact claim or hypothesis, per the two-assumption rule, in one sentence.
- **Sizing** — the reach times rate arithmetic and the detectable-effect floor.
- **Read plan** — primary metric, leading indicator if used, baseline window, read date, owner,
  and the events that must exist before ship.
- **What would falsify this** — the observation that would break the chain.

## Quality bar

- Every one of the four links carries an explicit status label.
- The business metric is one already on a reviewed dashboard, named as such.
- Each assumed link has a named test with a cost in days.
- The sizing arithmetic is shown, including the floor below which the effect is undetectable.
- The read date is compatible with the feedback-loop length in the product context.
- Instrumentation is listed as a pre-ship dependency with an owner, not a follow-up.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `pyramid-report` next unless the user redirects, so the chain travels with the
  finding.
- Invoke `insight-to-spec` next unless the user redirects, so instrumentation lands as pre-ship
  work with an owner.
- Invoke `impact-tracker` now to log the read date at the moment the decision is made.
  Mandatory gate: an unscheduled read never happens.
- If the verdict is hypothesis under the two-assumption rule, invoke `prior-evidence-check` on
  the assumed links instead.
- Stop here if the chain is unmeasurable and no instrumentation is planned.
