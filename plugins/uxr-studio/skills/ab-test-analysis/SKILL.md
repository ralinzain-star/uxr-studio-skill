---
name: ab-test-analysis
description: >
  This skill should be used when the user asks to "read the experiment results", "did the A/B
  test win", "the test came back flat, now what", or has an experiment readout that needs
  interpreting. Produces a results memo with sanity checks, interval-based reads, and a
  recommendation tied to the pre-registered decision rule.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# A/B Test Analysis

Report the confidence interval, never the point estimate alone. A non-significant result is a
bounded effect, not "no difference": the honest sentence is "the effect is somewhere between
minus 1.2 and plus 0.8 points", not "the variant did nothing".

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- An experiment has reached its planned stop and needs a ship or no-ship recommendation.
- A stakeholder is quoting a lift percentage with no interval attached.
- A test is flat on the primary and someone wants to go hunting in the segments.
- Do not use this when the experiment has not been designed or pre-registered yet. Use
  `ab-test-design` first, and `funnel-diagnostics` when there is no experiment at all.

## Gather first

1. The pre-registration: hypothesis, primary, guardrail, MDE, decision rule, planned stop.
2. Arm-level counts, the metric numerators and denominators, and the traffic split as designed.
3. Anything shipped, broken, or seasonal during the window.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Run the sanity checks before you look at the primary.** Reading the result first makes every
   later check negotiable.
   - **Sample ratio mismatch.** Compare observed arm sizes against the designed split with a
     chi-square test. If p is under 0.001, the assignment or logging is broken. Stop. Do not
     analyze, do not "adjust". Report the mismatch and fix the plumbing.
   - **Instrumentation.** Confirm the metric fires in both arms at a plausible baseline, and that
     the control's primary rate matches the historical baseline from `~~product analytics`. A
     control that drifted means the metric changed, not the product.
   - **Novelty and primacy.** Plot the daily effect. If the lift is concentrated in the first
     three days and decays, report the stabilized period as the estimate and say so.
   - **Exposure.** Verify only exposed users are in the denominator.
2. **Read the primary as an interval against the MDE**, not against zero. Four verdicts:
   the whole interval sits above the MDE, a win worth shipping; the interval straddles the MDE, a
   positive but undersized or underpowered result; the whole interval sits inside plus or minus
   the MDE, a real and useful null meaning the change does not matter at any size worth acting
   on; the interval includes harm, do not ship.
3. **Check the guardrail at its pre-set break threshold**, using the same interval logic. A
   guardrail that is directionally worse but whose interval stays inside the threshold has held.
4. **Cut segments only when the cut was pre-registered.** Post hoc cuts across plan, tenure,
   device, and geography will always produce one "significant" subgroup. If a cut is run anyway,
   label it exploratory, report it as a hypothesis for a new test, and never let it override the
   primary. The geography trap applies: a global read is dominated by the large non-converting
   share of traffic, so if geography was pre-registered, read the revenue-bearing segment
   separately rather than treating the pooled number as the truth.
5. **Handle a flat primary with a moving guardrail as a finding, not a failure.** It usually means
   the change shifted composition instead of behavior, for example more trial starts alongside
   more early cancellations, which is the same population routed differently. Say which mechanism
   you believe and what would confirm it. Do not ship on the guardrail's movement.
6. **Write the recommendation as a direct match to a decision-rule branch.** Quote the branch,
   then state the action. If the result fits no branch, say the rule was incomplete and name the
   missing branch rather than inventing a verdict now that the numbers are visible.
   **Hard stop: if the pre-registered decision rule from the design is not in hand, do not
   continue the analysis.** Ask for it with AskUserQuestion and wait. Never infer it, never
   write "TBD", and never proceed assuming it will arrive later. A rule written after the
   numbers are visible is not a decision rule, it is a rationalization, and it defeats the
   whole skill. The only exception is an explicitly unattended run, in which case emit the
   verdict `Blocked: no pre-registered decision rule` and nothing else.
7. **Record the effect ceiling for every null.** "We can rule out a lift larger than 1.1 points"
   is a durable result that stops the same test being re-run in six months.

## Output

- **Recommendation** — ship, do not ship, escalate, or extend, in one sentence, with the branch
  quoted.
- **Sanity checks** — each check, pass or fail, with the number.
- **Primary** — control rate, variant rate, absolute and relative difference, confidence interval,
  and the verdict against the MDE.
- **Guardrail** — same shape, against its break threshold.
- **Diagnostics** — what they suggest about mechanism, explicitly non-deciding.
- **Segments** — pre-registered cuts only, with exploratory ones fenced off and labelled.
- **What we can now rule out** — the effect ceiling.
- **Open questions** — what to test or interview next.

## Quality bar

- Sanity checks are reported before any outcome number, and SRM was tested, not eyeballed.
- Every metric appears as an interval; no bare point estimate or bare p-value stands alone.
- The primary is judged against the MDE, not against zero.
- No post hoc segment appears outside the exploratory section.
- The recommendation quotes a pre-registered branch, or names the branch that was missing.
- A null result states an effect ceiling.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now. Mandatory gate: no results memo reaches anyone before its
  claims are interrogated.
- Invoke `insight-writer` next unless the user redirects.
- Invoke `impact-tracker` now to log the ship decision; the study has closed, and an unlogged
  decision counts as zero impact.
- If the primary was null, invoke `research-repository-hygiene` to file the effect ceiling so
  the test is not re-run.
- If the memo names an unresolved mechanism, invoke `interview-guide-builder` instead of
  closing out.
