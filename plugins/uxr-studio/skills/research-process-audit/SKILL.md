---
name: research-process-audit
description: >
  This skill should be used when the user asks to "audit our research practice", "how mature is
  our research function", "why isn't our research landing", or needs an end-to-end assessment of
  how a team does research. Produces a scored audit across seven dimensions, the evidence behind
  each score, and one named highest-leverage fix.
metadata:
  version: "0.1.0"
  stage: "ops"
---

# Research Process Audit

Audit against decision throughput, not study volume. A team running more studies that change
fewer decisions is getting worse, and every vanity metric in research operations, studies
shipped, participants run, repository entries, will hide that. The deliverable is one fix, not
an inventory of everything imperfect.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A research function is being assessed, restructured, resourced, or defended.
- Research output is high and stakeholders still say they are not getting value.
- The same failure has shown up in three consecutive runs of `study-retro`.
- Do not use this when the subject is a single finished study — use `study-retro`.

## Gather first

1. The last 8 to 12 studies: question, method, cost, and the decision each fed.
2. Where research artifacts live and who can reach them: `~~research repository`,
   `~~project tracker`, `~~chat`.
3. Team shape: researchers, ratio to product teams, who else runs research unsupervised.
4. Two or three stakeholder accounts of a time research did and did not help them.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Compute decision throughput first, before scoring anything.** For the last 8 to 12 studies,
   count how many produced a dated, cited decision artifact. Report it as a ratio and as a trend
   against the prior period. If throughput fell while study count rose, that is the headline and
   everything else is detail.
   **Hard stop: if the last 8 to 12 studies and the decision each fed are not known, do not
   continue the audit.** Ask for the list with AskUserQuestion and wait. Never estimate
   throughput, never write "TBD", and never proceed assuming the studies will be supplied later.
   Every dimension score hangs off this ratio, and an estimated denominator turns the whole audit
   into an opinion. The only exception is an explicitly unattended run, in which case emit the
   verdict `Blocked: no study list to compute decision throughput` and nothing else.
2. **Score seven dimensions, 1 to 5, each against named evidence.** A score without evidence is
   an opinion and does not go in the audit.
   - **Intake and prioritization.** Evidence: how requests arrive, how many were declined or
     reshaped, whether anything was answered from existing evidence instead of a new study.
     Signal of a 2: every request becomes a study.
   - **Question quality.** Evidence: sample the briefs. Count how many name a decision and a
     changed action versus a topic. Signal of a 2: questions beginning "how do users feel about".
   - **Method and sampling rigor.** Evidence: sample frames of the last five studies. Check
     whether each named who it excluded. Use the sampling trap named in the product context as the
     worked example of a rigor failure: a monetization study drawn from the frame that trap
     describes produces a finding about people who were never going to pay.
   - **Cycle time.** Evidence: median days brief to report, and median days report to decision.
     The second number is the one nobody measures and the one that usually explains the problem.
   - **Synthesis and claim discipline.** Evidence: trace three claims in recent reports back to
     data. Check whether disconfirming cases appear anywhere.
   - **Distribution and reuse.** Evidence: repository search behavior, how often a new study
     restates a known finding, whether anyone outside the team cites past work.
   - **Democratized research safety.** Evidence: how much research non-researchers run, and
     whether guardrails exist. Signal of a 2: unsupervised surveys with leading questions going
     to customers.
3. **Look for the dominant constraint, not the lowest score.** Dimensions are coupled: slow
   cycle time is often caused by weak intake, and poor reuse is often caused by unclear claims.
   Trace backwards from the throughput number to the one dimension that gates the rest.
4. **Weight the report-to-decision gap heavily.** A team with excellent methods and a 40-day
   report-to-decision median is not a methods problem. It is a timing and stakeholder problem,
   and improving methods will make it worse by adding time.
5. **Write the maturity read as one sentence and one stage.** Four stages: ad hoc (research
   happens when asked), service (research is reliable and reactive), embedded (research shapes
   what gets asked), strategic (research sets agenda and is cited without the researcher in the
   room). Name the stage and the one behavior that would move it.
6. **Name exactly one highest-leverage fix.** One change, its owner, its cost, and the number it
   should move within one quarter. Everything else goes in an appendix marked "not now." Listing
   ten fixes guarantees zero: a team at capacity picks the easiest, and that is never the
   constraint.
7. **Set the re-audit date and the single tracking metric** at the time you write the fix, or it
   will not be checked.

## Output

- **Headline** — decision throughput ratio, trend, and the one-sentence maturity read.
- **Scorecard** — seven dimensions, score, and two lines of evidence each.
- **Dominant constraint** — the causal chain from the constraint to the throughput number.
- **The one fix** — change, owner, cost, the metric it moves, the target, the date.
- **Not now** — everything else, listed without recommendations attached.
- **Re-audit** — date and the single metric that will be re-measured.

## Quality bar

- Decision throughput is computed from named studies, not estimated.
- Every dimension score cites specific evidence, including at least one artifact per dimension.
- Report-to-decision median is measured separately from brief-to-report.
- Exactly one fix is recommended, with an owner, a metric, and a target date.
- The maturity read is a single stage plus a single behavior, not a paragraph.
- Nothing in the audit rewards study volume as a positive signal on its own.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If the dominant constraint is intake, invoke `research-intake-triage` now, then
  `research-brief-builder`.
- If it is claim discipline, invoke `so-what-checker` and then `insight-writer` instead.
- If it is unsupervised research, invoke `democratization-guardrails` instead.
- If it is findability or reuse, invoke `research-repository-hygiene` instead.
- Invoke `impact-tracker` next unless the user redirects, to open an entry for the one fix with
  its owner, its metric and the re-audit date.
- Stop here once that entry exists. Do not start a second fix this turn.
