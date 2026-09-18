---
name: workflow-mixed-methods
description: >
  This skill should be used when the user asks to "combine survey and interviews", "we need
  both numbers and reasons", "size this qualitative finding", or needs to sequence a study
  whose question requires qualitative and quantitative strands together. Produces the ordered
  chain, the sequence decision, the gate at each step, and the abort conditions.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Mixed Methods

Mixed methods is not two studies stapled together. One strand must answer a question the other
strand created, so the sequence decision is the whole design. Settle it before anything is
fielded. Never run both strands blind and reconcile afterwards.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- The question has a "how many" and a "why" that cannot be separated without losing the answer.
- A qualitative finding needs sizing, or a number needs explaining, before anyone will act.
- Do not use this when one strand alone would settle the decision. Use `workflow-discovery` or
  the relevant single-method skill. When the question is a funnel drop, use
  `workflow-conversion-diagnosis`, which already sequences both.

## Gather first

1. The decision, and which strand the decision-maker will actually believe.
2. Whether a list exists to survey, and whether it over-represents the sampling trap named in
   the product context.
3. The deadline, against the feedback-loop length in the product context.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## The chain

Realistic elapsed time: six to eight weeks sequential, four to five if the strands run in
parallel. Parallel is faster and weaker. Choose it only when the deadline forces it.

1. `research-question-sharpener` — consumes the request. Produces the question split into its
   quantitative part and its qualitative part. Gate: both parts name what result changes the
   decision. **Abort gate:** if only one part survives, drop the other strand and say so. Half
   of all mixed-methods requests are a single-method study wearing a bigger budget.
2. `mixed-methods-designer` — consumes the split question. Produces the sequence:
   qualitative-first (explore, then size), quantitative-first (locate, then explain), or
   parallel. Produces the integration plan and what each strand owes the other. Gate: the
   integration plan is written before fielding, not after.
3. **Strand A, quantitative.** `survey-builder` consumes the quantitative question and, in a
   qualitative-first design, the language participants actually used. Produces the instrument.
   Gate: piloted with five people, no leading or double-barrelled items, wording checked against
   the product context's tone rules. `survey-analysis` then produces sized findings with
   intervals. Gate: response base and non-response bias stated.
4. **Strand B, qualitative.** `interview-guide-builder` consumes the qualitative question and, in
   a quantitative-first design, the number to be explained. Produces the guide. `thematic-coding`
   then produces a codebook with counts. Gate: codebook frozen before the last third of
   transcripts, intercoder agreement checked if more than one person codes.
5. **Parallel note.** Steps 3 and 4 run concurrently only in a parallel design. Sequentially the
   second strand cannot start until the first strand's gate passes, because its content comes
   from that output.
6. `triangulation` — consumes both strands. Produces agreements, disagreements and an
   explanation for each disagreement. Gate: at least one disagreement examined, not averaged
   away. **Abort gate:** if the strands contradict and no design difference explains it, report
   the contradiction as the finding and commission nothing further.
7. `insight-writer` — consumes triangulated results. Produces insights that state the size and
   the mechanism in one sentence. Gate: no insight rests on one strand alone unless labelled.
8. `so-what-checker` — consumes insights. Produces the kept set. Gate: each survives without its
   percentage attached.
9. `pyramid-report` — consumes the kept set. Produces the report, answer first, strands as
   evidence not chapters. Gate: a reader who stops after page one has the answer.

## Checkpoints

- After step 2: sequence and integration plan written and agreed.
- Before fielding: the survey instrument piloted, the guide piloted.
- After step 6: disagreements explained, not smoothed.
- After step 9: the report is organized by answer, not by method.

## Quality bar

- The sequence choice is justified by the question, not by scheduling convenience.
- Each strand's sample is described separately, including geography and base.
- Numbers and quotes appear in the same claim, not in separate sections.
- No qualitative theme is reported as a percentage of a small sample.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-shareout` after the final step to run the report as a decision meeting.
- Invoke `insight-to-spec` for each actionable finding, then `impact-tracker` for the decision.
- Invoke `research-question-sharpener` now to begin: it is step 1 of the chain above. Work
  the steps in order in this same turn, without pausing for approval between them, stopping
  only at a declared abort gate.
