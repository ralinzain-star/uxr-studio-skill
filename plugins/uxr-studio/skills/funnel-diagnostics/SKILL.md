---
name: funnel-diagnostics
description: >
  This skill should be used when the user asks "where are we losing people", "why is
  conversion dropping", "break down the funnel", or needs to locate and rank drop-off points
  before commissioning qualitative work. Produces a ranked drop-off list with cohorted rates
  and a scoped qualitative study for the top one.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# Funnel Diagnostics

A funnel tells you where users leave and never why. The deliverable of this skill is therefore
always two things: a ranked list of drop-off points, and the qualitative study that will explain
the top one. A funnel analysis that ends in a product recommendation is overreaching, and the
recommendation will be a guess dressed in a percentage.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Conversion moved and nobody can say at which step.
- A team wants to fix a stage without establishing that it is the largest loss.
- A vague "activation is bad" needs turning into a ranked, cohorted set of candidate failures.
- Do not use this when the funnel is already ranked and you need to explain the top loss with
  users. Go straight to `churn-interview` or `interview-guide-builder`.

## Gather first

1. The funnel's business purpose and the step someone is already blaming.
2. Event and subscription data in `~~product analytics` and `~~data warehouse`, and whether
   cohorting by entry date is possible.
3. The window, and the product's natural cycle length (billing interval, usage cadence).
4. Known instrumentation gaps or event renames inside the window.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Define steps so they are mutually exclusive and ordered.** Each user sits in exactly one
   step at a time and can only move forward. If a user can sit in two steps at once, or can
   reach step 4 without step 3, the funnel is a Venn diagram and its percentages are
   meaningless. Write each step's event definition and property filters beside its name.
2. **Choose the denominator on purpose and state it.** Step-to-step conversion (of those who
   reached step N, how many reached N+1) diagnoses a step. Cumulative conversion (of everyone who
   entered, how many reached step N) sizes the opportunity. Report both, label which is which, never
   mix them in one chart. Quoting a step rate as if it were an overall rate is the common mistake
   and it inflates every projected impact.
3. **Cohort by entry date, never by calendar activity.** Group users by the date they entered the
   funnel and follow that group forward. Mixing tenures means a change in traffic mix reads as a
   change in conversion. Cohorts must be old enough to have resolved. Set the maturity bar from
   the feedback-loop length in the funnel section of the product context, and state it: a window
   shorter than that loop shows a fake improvement made entirely of immature cohorts.
4. **Separate cannot from chose not.** At every step, split the drop into a blocked path (error,
   payment decline, dead end, unsupported state) and a declined path (the user reached the
   decision and said no). These have different owners and different fixes. Blocked drops are
   usually instrumented and fixable without interviews. Declined drops need users.
5. **Disaggregate any aggregate number before believing it.** One rate averaged over two unrelated
   failure modes points at a fix for neither. Build the worked example from the known problem
   areas in the product context: take the headline loss the context flags as two problems wearing
   one number, name each sub-population and the share the context gives it, and show that the two
   need different evidence. A chose-not loss is a value and expectation problem answered by
   talking to people. A blocked loss is a billing or plumbing problem answered by logs and
   dunning. Averaged, they produce a roadmap that helps neither group. Before ranking any step,
   ask what two populations could be hiding inside it, and split them.
6. **Rank by absolute users lost, not by percentage.** A 70% drop on a step 2% of users reach is
   smaller than a 12% drop at the top. Compute users lost per step per cohort, percentage
   beside it as context.
7. **Check the plumbing before the psychology.** An unexplained cliff is an instrumentation bug
   until proven otherwise. Rule out event renames, bot-filtering changes and tracking outages.
8. **Scope the qualitative follow-up for the top ranked loss only.** Name the population as a
   re-runnable cohort definition, the method, and the question it answers. One study, not three.

## Output

- **Funnel definition** — table: step, event definition, filters, mutual-exclusivity note.
- **Cohort and window** — entry-date rule, maturity required, why.
- **Rates table** — per step: entered, advanced, step rate, cumulative rate, users lost.
- **Cannot vs chose not** — per step, the split and how each side was identified.
- **Disaggregation check** — sub-populations tested inside each large loss, and what was found.
- **Ranked drop-off list** — ordered by users lost, one line each on what is and is not known.
- **The one qualitative study** — question, population as a cohort definition, method, sample
  size, and what result would change the decision.
- **Data caveats** — instrumentation gaps, immature cohorts, definitional choices.

## Quality bar

- Every step has a written event definition and the steps cannot overlap.
- Step and cumulative rates are both present and separately labelled.
- Cohorts are keyed to entry date and are old enough to have resolved.
- Each ranked loss is split into blocked versus declined, or says explicitly it could not be.
- The largest loss was tested for two hidden populations, not accepted as one number.
- The document ends in a study, not a product recommendation.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now on the scoped study. Mandatory gate: the top loss may
  already have been explained.
- Invoke `research-question-sharpener` next unless the user redirects, on the study question.
- Invoke `sampling-plan` next unless the user redirects, so the sample matches the cohort that
  actually dropped.
- If the top loss is a trial or retention loss, invoke `churn-interview`; if it is in-product,
  invoke `usability-test-plan`.
- If the largest loss is a blocked path, invoke `insight-to-spec` instead; that is an
  engineering ticket, not a study.
- Stop here if no declined-path loss remains.
