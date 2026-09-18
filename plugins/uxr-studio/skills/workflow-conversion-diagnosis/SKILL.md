---
name: workflow-conversion-diagnosis
description: >
  This skill should be used when the user asks "why are people dropping off here", "conversion
  fell and we don't know why", "diagnose this funnel step", "why aren't trials converting", or
  needs to explain a specific drop and fix it. Produces the ordered chain from funnel
  decomposition through per-mode investigation to a tested fix, with the gate at each step.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Conversion Diagnosis

A drop-off rate averages unrelated failures, and the critical move is splitting it before
investigating it. Research the aggregate and you get a roadmap that helps none of the populations
inside it. Split first, then send each mode to its own instrument.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A funnel step is losing people, or a rate moved and the cause is unclear.
- A team is about to build a fix for a drop-off they have not decomposed.
- Do not use this when one mode is already isolated and understood. Go straight to its instrument.
  With no number to explain, use `workflow-discovery`.

## Gather first

1. The step, the current rate, the measurement window.
2. What is believed about the cause, and by whom. Beliefs decide which modes get looked at.
3. Access to `~~product analytics`, `~~data warehouse`, `~~support desk`.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the rest
from the product context and say what you inferred.

## The chain

Realistic elapsed time: six to eight weeks to a shipped, tested fix. Steps 3a to 3c and step 4 run
in parallel, which is what keeps it under ten. Check the feedback-loop length in the product
context before promising a read date on the experiment.

1. `funnel-diagnostics` — consumes the step and window. Produces exclusive step definitions,
   cohorted rates, users lost per step. Gate: cohorts keyed to entry date and mature enough to have
   resolved. **Abort gate:** if the drop coincides with an event rename, tracking outage,
   bot-filter change or definitional edit, treat it as an instrumentation artifact. Stop, hand it
   to engineering, re-measure. Interviewing about an unreal number always ends in a plausible
   story.

2. **The split check. This is the critical step.** Ask what structurally different populations sit
   inside the ranked loss. Do not invent them: the known problem areas in the product context name
   the modes hiding inside this product's aggregate rates. Cross-check cancellation reasons and
   tickets in `~~support desk`. Produce a table of mode, queryable cohort definition, share, owner,
   instrument. Gate: each mode is countable in `~~data warehouse`; an unqueryable mode is a
   hypothesis and goes to step 4 first. Gate: no mode is dropped for belonging to another team.

3. **Per mode, the right instrument. Run in parallel.**
   - **3a. Deliberate abandoners**, who decided and said no: `churn-interview` on that cohort.
     Gate: recruited from the mode while they still remember, under the context's tone rules.
   - **3b. Blocked users**, who could not proceed: `usability-test-plan` for comprehension and
     interaction blocks, a `funnel-diagnostics` follow-up on error and replay data for dead ends.
     Gate: the block reproduced before it is explained.
   - **3c. Non-behavioral failures**, payment or eligibility: hand off via `insight-to-spec` with
     owning team, volume and evidence. Gate: owned outside research scope.

   Routing test and per-mode recruiting traps: `references/mode-instruments.md`.

4. `segmentation-analysis` — sizes each mode by the segments in the product context and states
   geography, because the sampling trap named there distorts any share drawn from raw traffic.
   Gate: shares reconcile, or the remainder is named.

5. `triangulation` — consumes accounts and sizes. One explanation per mode, disagreements marked.
   Gate: no mode explained by evidence without a size, or by size without a mechanism.

6. `insight-writer` — one insight per mode: population, size, mechanism, consequence. Gate: none
   spans two modes.

7. `so-what-checker` — produces the kept set. **Abort gate:** if the largest mode has no
   actionable mechanism, recommend instrumentation, not a fix.

8. `pyramid-report` — answer first, one section per mode. Gate: the aggregate never appears
   without its decomposition.

9. `ab-test-design` — consumes the top mode's mechanism. One experiment, one mode, primary metric
   on that cohort. Gate: powered for the mode, not the funnel.

10. `impact-tracker` — logs the decision and a revisit date set against the product context's
    feedback-loop length.

## Checkpoints

- After step 1: the number is real, and a matured cohort says so.
- After step 2: every mode is separately countable and separately owned.
- After step 9: the experiment is powered on the mode, not the funnel.

## Quality bar

- The aggregate was split before any participant was recruited.
- The modes came from the product context and the ticket data, not the analyst.
- Non-behavioral modes left research scope with a named owner.
- Every mode carries a size and a mechanism, or is labelled as missing one.
- The instrumentation abort gate was evaluated and recorded.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `ab-test-analysis` after the final step to read the experiment against its pre-registered
  rule.
- If a mode was sized but left unfixed, invoke `opportunity-backlog` for it.
- Invoke `research-repository-hygiene` to file the study once the fix ships.
- Invoke `funnel-diagnostics` now to begin: it is step 1 of the chain above. Work the steps in
  order in this same turn, without pausing for approval between them, stopping only at a
  declared abort gate.
