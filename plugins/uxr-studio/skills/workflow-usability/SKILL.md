---
name: workflow-usability
description: >
  This skill should be used when the user asks to "run a usability test", "test this design",
  "watch users try the new flow", or needs to sequence a task-based evaluation from brief to
  shipped fix. Produces the ordered chain of skills, the gate at each step, the branch to
  quantitative metrics, and the abort conditions.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Usability

A usability study evaluates a design against tasks people already want to do. It cannot tell you
whether the feature should exist, and drifting into that question wastes both studies. Keep the
chain short, the tasks real, and end in a spec rather than a list of nits.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A design, prototype or live flow exists and the team needs to know where people fail.
- Do not use this when no design exists, or the team is arguing about which problem to solve.
  Use `workflow-discovery`. When a funnel number moved, start with
  `workflow-conversion-diagnosis` so the right step gets tested.

## Gather first

1. What the design team will decide with the result, and by when.
2. What the user is trying to accomplish, in their words, not the feature name.
3. Whether the artifact is clickable enough to fail realistically.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## The chain

Realistic elapsed time: two to three weeks for five to eight moderated sessions, about one week
of that recruiting. The topline lands within two days of the last session.

1. `research-brief-builder` — consumes the request and the design. Produces the brief: decision,
   tasks, success definition. Gate: the design owner agrees these are the real tasks.
   **Abort gate:** if the design ships unchanged whatever happens, stop and say so in writing.
2. `usability-test-plan` — consumes the brief. Produces tasks, scenarios and success criteria.
   Gate: every task is a goal with a starting context, never an instruction naming the control to
   click. Anchor one task in a standing problem area from the product context.
3. `sampling-plan` — produces population and quotas, respecting the sampling trap named in the
   product context. Gate: participants plausibly have the task's goal.
4. `screener-builder` — consumes it. Produces the screener. Gate: no question reveals the study.
5. `participant-comms` — consumes the screener. Produces invites and consent under the product
   context's tone rules. Gate: first session booked. **Runs in parallel with guide piloting.**
6. `interview-moderation` — consumes the plan. Produces sessions. Gate: pilot run and task
   wording fixed before session two.
7. `session-debrief` — consumes each session within the hour. Produces failure notes tagged to
   task. Gate: nothing waits for the end of the round.
8. `lightning-synthesis` for five to eight sessions, `affinity-synthesis` beyond about twelve or
   across segments. Consumes debriefs. Produces failures ranked by severity and frequency. Gate:
   severity argued from consequence, not from how loud the participant was.
9. `insight-writer` — consumes ranked failures. Produces insights naming behavior, cause and
   consequence. Gate: each is testable against a redesign.
10. `so-what-checker` — consumes insights. Produces the kept set. **Abort gate:** if every
    finding is cosmetic, say the design passed. Never manufacture severity to justify the study.
11. `topline-writer` — consumes the kept set within two days. Produces the short read while the
    design work is still moving.
12. `insight-to-spec` — consumes the topline. Produces owned changes, each with an owner and a
    ticket in `~~project tracker`.
13. `impact-tracker` — consumes shipped changes. Produces the decision log entry and a revisit
    date honoring the feedback-loop length in the product context.

**Quant branch.** If anyone asks for completion rates, time on task or a before-and-after number,
insert `quant-usability-metrics` between steps 2 and 3 and route sample size through
`sample-size-advisor`. Rounds of five to eight produce no rates. Say so, then resize to an
unmoderated study or refuse the number. A percentage from eight people outlives the study.

## Checkpoints

- After step 1: the decision is live and reversible.
- After the pilot: task wording fixed, not defended.
- After step 13: the fix is verified in `~~product analytics`, or a revisit date is set.

## Quality bar

- Tasks describe goals, never interface elements.
- Sample size and any reported number are consistent with each other.
- Severity ranking survives the question "what does this cost the user".
- The topline shipped within two days of the last session, and each insight has an owner.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If the fix needs rate-based confirmation, invoke `ab-test-design` after the final step.
- Invoke `research-repository-hygiene` to file the study, then `study-retro` to close it.
- Invoke `research-brief-builder` now to begin: it is step 1 of the chain above. Work the steps in
  order in this same turn, without pausing for approval between them, stopping only at a
  declared abort gate.
