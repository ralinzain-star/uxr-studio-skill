---
name: ab-test-design
description: >
  This skill should be used when the user asks to "design an A/B test", "set up an experiment",
  "how long should we run this test", or wants to validate a product change with live traffic.
  Produces a pre-registration document with hypothesis, metrics, power calculation, duration,
  invalidation conditions, and a decision rule.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# A/B Test Design

State the smallest effect worth shipping before computing anything. A test powered for an effect
nobody would act on burns traffic to produce a shrug. Pick exactly one primary metric and one
guardrail, because a test with five primary metrics always wins.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A change is built or buildable and the team needs evidence it moves a metric.
- Two designs are deadlocked and someone wants live traffic to settle it.
- A previous test is being re-run and the design needs tightening first.
- Do not use this when the question is why users behave a certain way, or when the change has not
  been designed yet. Use `interview-guide-builder` or `concept-test-design` instead, and
  `funnel-diagnostics` when the team cannot yet name where the loss happens.

## Gather first

1. The change, stated precisely enough that a stranger could tell which arm they are in.
2. The baseline rate of the candidate primary metric and the traffic available to the surface.
3. The smallest effect the team would actually ship for, and what they do if the effect is half
   that size.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the hypothesis in one fixed form.** "Because [evidence], we believe [change] will
   cause [primary metric] to [direction] by at least [MDE] for [population], measured over
   [window]." If the "because" is empty, the test is a guess and should be cheaper.
2. **Set the minimum detectable effect by asking what the team does at each size.** Walk three
   sizes: the hoped-for lift, half of it, and a quarter. The MDE is the smallest size that still
   produces "ship it". Record that conversation. This is the single step teams skip and the
   reason most tests are unreadable.
   **Hard stop: if the smallest effect the team would actually ship for is not known, do not
   continue the design.** Ask for it with AskUserQuestion and wait. Never infer it, never write
   "TBD", and never proceed assuming it will arrive later. Every number downstream — power,
   sample, duration, the decision rule — is computed from this one, so guessing it silently
   corrupts the whole pre-registration. The only exception is an explicitly unattended run, in
   which case emit the verdict `Blocked: no minimum effect worth shipping` and nothing else.
3. **Make the randomization unit equal the exposure unit and the analysis unit.** Default to
   user, persisted across sessions and devices where a login exists. On anonymous marketing
   traffic, randomize by device and state the contamination, since one person can cross arms
   between the anonymous site and the authenticated product.
4. **Name one primary, one guardrail, up to four diagnostics.** The primary carries the decision.
   The guardrail is what the change could plausibly break and must not. Diagnostics explain a
   result, never decide it. Example: a paywall change takes trial start rate as primary,
   cancellation within 14 days as guardrail, scan completion and time-to-first-scan as
   diagnostics.
5. **Prefer a leading metric with a fast loop over a lagging one you cannot wait for.** With
   quarterly plans and a trial in front of them, the trial-to-renewal loop runs about 90 days, so
   renewal cannot be a primary metric in a two-week test. Take the nearest trustworthy leading
   indicator as primary and schedule a lagging cohort read for the same users later.
6. **Compute power, then duration.** Two-sided, alpha 0.05, power 0.80. For a proportion metric,
   sample per arm is roughly 16 × p × (1 − p) ÷ d², where p is baseline and d the absolute lift.
   Example: baseline trial start 10%, MDE a 10% relative lift, so d = 0.01, giving about 14,400
   users per arm. Then duration is the larger of the traffic requirement and two complete
   business cycles, minimum 14 days. Never stop early on a peek, and never end mid-week.
7. **List what invalidates the test before it starts.** Sample ratio mismatch, a release into
   either arm mid-flight, instrumentation gaps, an overlapping test on the same surface, a
   seasonal spike, any assignment leak. Name the owner who checks each.
8. **Pre-register the decision rule with an entry for the null.** Four branches: primary clears
   MDE and guardrail holds, ship. Primary clears MDE and guardrail breaks, escalate with the
   trade-off named. Primary is flat or the interval excludes the MDE, do not ship and record the
   effect ceiling. Primary is inconclusive and the interval still spans the MDE, extend once by a
   pre-stated amount or abandon. Write what happens, not what will be discussed.

## Output

A pre-registration document in this order: hypothesis; population and exclusions; randomization
unit and split; primary metric with exact definition and baseline; guardrail with its break
threshold; diagnostics; MDE and the reasoning from step 2; power calculation and n per arm; start
date, minimum duration, planned stop date; invalidation conditions with owners; the four-branch
decision rule; and the lagging follow-up read if there is one.

## Quality bar

- The MDE was set before the power calculation, and the write-up shows why.
- Exactly one primary metric and exactly one guardrail appear, each with an executable definition.
- Randomization, exposure, and analysis units are the same unit, or the mismatch is stated.
- Duration covers two full business cycles, and no metric needs a loop longer than the window.
- The decision rule names an action for a null result.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: the pre-registration is not fielded on
  live users until it clears.
- Invoke `decision-metrics-mapper` next unless the user redirects, to confirm the primary sits
  on a chain someone already watches.
- If the team cannot yet name the surface where the loss happens, invoke `funnel-diagnostics`
  instead of launching.
- If the team also needs to know why the metric moved, invoke `mixed-methods-designer`.
- Stop here until the test reaches its planned stop; invoke `ab-test-analysis` then.
