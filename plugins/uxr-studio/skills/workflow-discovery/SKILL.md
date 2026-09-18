---
name: workflow-discovery
description: >
  This skill should be used when the user asks to "run a discovery study", "we don't know
  what the problem is", "do some generative research", "explore this space", or needs to
  sequence an open-ended qualitative study end to end. Produces the ordered chain of skills,
  the gate that must pass at each step, and the abort conditions.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Discovery

Discovery is the longest chain here and the easiest to fake, because nothing in it fails loudly.
Run it only when no one can name the problem, and gate every step on a written artifact rather
than on a feeling that enough people have been talked to.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A team can name a worry but not a question, or is arguing about which problem is real.
- Someone asks "what should we research?" with no candidate. Anchor on the standing problem
  areas in the product context rather than inventing a topic.
- Do not use this when the question is already sharp and testable against a design. Use
  `workflow-usability`. When a number is already moving, use `workflow-conversion-diagnosis`.

## Gather first

1. The decision this informs and its owner. Check the decisions section of the product context first.
2. The date the answer stops being useful.
3. Whether the team will accept a qualitative answer with no rate attached.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## The chain

Realistic elapsed time: five to seven weeks, roughly two of them recruiting. Compress by cutting
sample, never by cutting debriefs.

1. `research-intake-triage` — consumes the request as stated. Produces a decision, owner and
   deadline. **Abort gate:** if no decision is attached, or it is already made, stop and say so.
2. `prior-evidence-check` — consumes the decision. Produces what is already known from the
   evidence sources named in the product context. Gate: the question survives. **Abort gate:**
   if the answer exists, write it up and end the chain here.
3. `research-question-sharpener` — consumes the surviving gap. Produces two to four answerable
   questions. Gate: each names what result would change the decision.
4. `method-selector` — consumes the questions. Produces the method and why the cheaper
   alternatives were rejected. Gate: generative method chosen deliberately, not by default.
5. `sampling-plan` — consumes the questions. Produces population, quotas and exclusions,
   honoring the sampling trap named in the product context. Gate: quotas justified by expected
   difference, not convenience.
6. `screener-builder` — consumes the sampling plan. Produces the screener. Gate: no question
   telegraphs the desired answer.
7. `participant-comms` and `interview-guide-builder` — **run in parallel.** Comms consumes the
   screener, produces invites and consent under the product context's tone rules. The guide
   consumes the sharpened questions. Gate: first session booked, guide piloted once.
8. `interview-moderation` — consumes the guide. Produces sessions. Gate: guide revised after
   session two if it is not earning stories.
9. `session-debrief` — consumes each session within the hour. Produces per-session notes.
   Gate: no second session runs before the first is debriefed.
10. `affinity-synthesis` — consumes all debriefs. Produces themes with evidence counts. Gate:
    every theme traces to at least three participants or is labelled a signal, not a finding.
11. `insight-writer` — consumes themes. Produces insights. Gate: each states a behavior and a
    consequence, not a preference.
12. `so-what-checker` — consumes insights. Produces the kept set. **Abort gate:** if nothing
    survives, report that honestly instead of promoting the strongest weak finding.
13. `pyramid-report` — consumes surviving insights. Produces the report. Gate: answer first.
14. `opportunity-backlog` — consumes the report. Produces ranked opportunities with owners.
15. `impact-tracker` — consumes them. Produces the decision log entry. Gate: a revisit date set
    against the feedback-loop length in the product context.

## Checkpoints

- After step 2: go or stop, in writing.
- After step 9: saturation check. Stop recruiting when two consecutive sessions add no new theme.
- After step 12: the honest finding, including "we found nothing decision-grade".
- After step 15: a revisit date exists, or the study is not finished.

## Quality bar

- Every step names its input artifact and the gate that released it.
- Recruiting started no later than the day guide drafting began.
- Each abort gate was evaluated out loud, not skipped.
- No step was marked complete without the artifact the next step consumes.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `insight-to-spec` after the final step for each opportunity a team has agreed to build.
- Invoke `research-repository-hygiene` to file the study, then `study-retro` to close it.
- Invoke `research-intake-triage` now to begin: it is step 1 of the chain above. Work the steps in
  order in this same turn, without pausing for approval between them, stopping only at a
  declared abort gate.
