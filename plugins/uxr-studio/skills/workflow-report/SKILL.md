---
name: workflow-report
description: >
  This skill should be used when the user asks to "write up the research", "turn this analysis
  into a report", "share the findings", or has completed analysis and needs it to reach a
  decision. Produces the ordered chain from raw analysis to logged decision, with the gate at
  each step and the abort conditions.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Report

A report is not the end of a study. It is the instrument that converts analysis into a decision,
and it has failed if the decision does not move. Write the answer first, cut everything that does
not change what someone does on Monday, and do not finish the chain until a decision is logged.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Analysis is complete and the findings need to reach people who can act.
- A study has been sitting unread and needs repackaging for a specific decision.
- Do not use this when the analysis itself is unfinished. Go back to `affinity-synthesis` or
  `thematic-coding`. For a same-week read while sessions are still running, use `topline-writer`
  alone, not this chain.

## Gather first

1. The decision on the table, its owner, and the date it is made. Check the decisions section of
   the product context before asking.
2. Who reads and who decides. They are rarely the same person.
3. What the audience already believes, so the report can address it directly.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## The chain

Realistic elapsed time: four to seven working days from finished analysis to logged decision.
Most of that is scheduling the shareout, not writing.

1. `insight-writer` — consumes analysis output. Produces insights stating behavior, cause,
   consequence and confidence. Gate: each insight is one sentence a stakeholder could repeat.
2. `so-what-checker` — consumes insights. Produces the kept set and the reason each was cut.
   **Abort gate:** if fewer than two insights survive, do not write a report. Send a one-paragraph
   note saying what was looked at and what was not found, and return the budget. A thin report
   costs more credibility than an honest null.
3. `pyramid-report` — consumes the kept set. Produces the full report: answer, because-of
   reasons, evidence beneath each. Gate: the first page stands alone.
4. `topline-writer` — consumes the report. Produces the short version for people who will never
   open the full one. **Runs in parallel with step 5's scheduling.**
5. `research-shareout` — consumes the report and topline. Produces the session design: who is in
   the room, what decision is being asked for, what the discussion is. Gate: the decision-maker
   confirmed attending. A shareout without the decision-maker is a rehearsal.
6. `insight-to-spec` — consumes the decisions made in the room. Produces specific changes with
   owners and tickets in `~~project tracker`. Gate: each has a named owner, not a team.
7. `opportunity-backlog` — consumes findings that did not convert to immediate specs. Produces
   ranked, dated opportunities so nothing is quietly lost. Gate: every surviving insight is either
   a spec or a backlog item.
8. `research-repository-hygiene` — consumes all artifacts. Produces the filed, tagged study in
   `~~research repository`. **Runs in parallel with steps 6 and 7.** Gate: findable by someone
   who does not know the study existed.
9. `impact-tracker` — consumes specs and decisions. Produces the log entry and a revisit date set
   against the feedback-loop length in the product context. Gate: the chain is not complete until
   this entry exists.

## Checkpoints

- After step 2: enough survives to be worth a report, or the chain stops honestly.
- After step 3: the answer is on page one, above the method.
- After step 5: a decision was made in the room, or a date was set for it.
- After step 9: a logged decision with an owner and a revisit date.

## Quality bar

- The report opens with the answer, not with the background or the sample.
- Every claim carries its evidence base and its confidence.
- Nothing in the report exists only because the work was done.
- Each surviving insight ends as a spec or a dated backlog item.
- The decision, not the deck, is what the chain reports as its output.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If a theme recurs across studies, invoke `research-newsletter` for it.
- Invoke `study-retro` to close the study, and `research-roi` at the quarter boundary with the
  logged decisions.
- Invoke `insight-writer` now to begin: it is step 1 of the chain above. Work the steps in
  order in this same turn, without pausing for approval between them, stopping only at a
  declared abort gate.
