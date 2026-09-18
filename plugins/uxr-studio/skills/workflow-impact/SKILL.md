---
name: workflow-impact
description: >
  This skill should be used when the user asks to "show the value of research", "justify the
  research headcount", "what has research actually changed", "build the case for more
  researchers", or needs to demonstrate and grow the function's impact. Produces the ordered
  chain from decision log to business case, with the gate at each step.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Impact

Research impact is measured in decisions changed, not studies delivered or stakeholders
satisfied. Run this chain on a standing quarterly cadence rather than the week before budget
season, because the evidence it needs can only be captured while decisions are being made.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Leadership asks what research has delivered, or the budget is being questioned.
- A quarterly or annual review needs a defensible account of value, or the next hire has to be
  argued for.
- Do not use this when a single study needs writing up. Use `workflow-report`. When the question
  is whether a specific process is broken, run `research-process-audit` on its own.

## Gather first

1. The audience and what they dispute: that research changes decisions, or that the changes are
   worth the cost. These need different arguments.
2. The period under review. Use the decisions section of the product context for the categories.
3. Whether the outcome metrics are governed and available in `~~data warehouse`.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## The chain

Realistic elapsed time: two to three weeks if the decision log has been maintained, six or more
if it is being reconstructed. That gap is the reason to run steps 1 and 6 continuously.

1. `impact-tracker` — consumes the period's studies. Produces the decision log: study, decision,
   what changed, who decided, outcome metric, date. Gate: each entry names a decision that
   differed from the counterfactual. **Abort gate:** if fewer than a handful of entries name a
   real change, stop building a case and run `research-process-audit` first. The problem is
   upstream, and a case built on weak entries invites exactly the scrutiny it cannot survive.
2. `decision-metrics-mapper` — consumes the log. Produces the link from each decision to a
   governed metric and the attribution logic for it. Gate: attribution is stated as
   contributed-to, never caused, unless an experiment supports the stronger claim.
3. `research-roi` — consumes the mapped decisions. Produces the value account: costs avoided,
   value created, cost of the function, confidence per figure. Gate: the weakest number is
   labelled, not buried. Use the feedback-loop length in the product context to exclude outcomes
   that have not resolved rather than estimating them.
4. `research-process-audit` — consumes the log and the roster of requests. Produces where the
   practice loses value: unread reports, late intakes, studies with no decision attached.
   **Runs in parallel with steps 2 and 3.** Gate: at least one internal failure is named.
   A case that claims the function is already perfect asks for nothing and gets nothing.
5. `business-case-builder` — consumes the ROI account and the audit. Produces the ask: what
   changes, what it costs, what it returns, what happens without it. Gate: one specific ask with
   a number, not a general plea for support.
6. `research-newsletter` — consumes the log continuously. Produces the running narrative that
   makes step 5 unsurprising. **Runs on its own cadence throughout.** Gate: it reports decisions
   and outcomes, not activity. A newsletter listing studies completed teaches the audience to
   value volume, which is the argument you will later have to unmake.

## Checkpoints

- After step 1: enough real decision entries to argue from, or the chain stops and fixes process.
- After step 3: outcomes traced to governed metrics, unresolved ones excluded.
- After step 5: one specific ask with a number and a stated consequence of refusal.

## Quality bar

- Every impact claim names the decision, the decider and the date.
- No outcome is claimed where the feedback loop has not yet closed.
- Attribution language matches the evidence strength, claim by claim.
- The case names at least one thing the function does badly.
- The ask is specific enough to be approved or refused, not deferred.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-roadmap` after the final step to fold any accepted process fix into the next
  quarter.
- If the case is refused, invoke `research-process-audit` to find what the log could not show.
- Invoke `impact-tracker` now to begin: it is step 1 of the chain above. Work the steps in
  order in this same turn, without pausing for approval between them, stopping only at a
  declared abort gate.
