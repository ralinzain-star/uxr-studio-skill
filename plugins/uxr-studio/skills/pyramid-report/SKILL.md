---
name: pyramid-report
description: >
  This skill should be used when the user asks to "write up the research", "draft the report",
  "put together the findings deck", or needs to structure a full study writeup for people who
  can act on it. Produces a top-down report that opens with the answer and the recommendation
  and puts method at the back.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# Pyramid Report

Lead with the answer, then the recommendation, then the arguments, then the evidence. A report
that builds to its conclusion loses the only readers who can act on it, because those readers
stop at page two. Method and limitations go at the back. They are credentials, not content.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study is synthesised and needs a written deliverable an executive will read.
- Findings exist across several sessions or methods and must be ordered into one argument.
- A previous writeup was organised by session, week, or research activity and nobody acted on it.
- Do not use this when the last session ended in the past day and stakeholders are waiting.
  Ship `topline-writer` first, then come back to this.

## Gather first

1. The decision this report is meant to inform, and who owns that decision by role.
2. The synthesised findings, with their evidence strength, from `affinity-synthesis`,
   `thematic-coding`, or `triangulation`.
3. The method, sample, and where the sample departs from the population of interest.
4. The date by which the decision gets made with or without this report.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the answer as one sentence before anything else.** It must name a subject and a
   verdict, be falsifiable, and fit on one line. If the one-liner takes two sentences, the study
   answered two questions and needs two reports, or one of the questions was not actually answered.
2. **Write the recommendation next, with a named owner and a date.** A recommendation without a
   role attached is a wish. If no role can be named, the finding is not yet actionable and belongs
   in `opportunity-backlog` rather than in a report.
3. **Derive three to five supporting arguments, not more.** Each argument is a claim in a full
   sentence that would, on its own, move a reasonable skeptic partway to the answer. Six or more
   arguments means the answer is weak and is being propped up by volume. Two means the answer is
   an observation.
4. **Back each argument with evidence, and label the strength inline.** Give the count, the
   segment, the method that produced it, and one verbatim or one number. State how many
   participants or events the claim rests on directly, never as "users said".
5. **Apply the pyramid test to each argument.** Reading only the argument headings in order must
   reconstruct the answer. If the headings read as topics rather than claims, rewrite them as
   claims.
6. **Report what would have changed the answer.** Name the disconfirming result you looked for
   and did not find. This is the single cheapest thing that makes a report survive challenge.
7. **Put method, sample, and limitations at the back.** A report that opens with methodology
   signals that the researcher is defending the work rather than delivering it. Write limitations
   as what the study cannot be used to claim, not as an apology.
8. **Quote the standing problem area this report advances.** Use the known problem areas section
   of the product context to place the study in the running agenda, and use its decisions section
   to name which decision the recommendation feeds.
9. **Ban the chronological report.** Never organise sections by session, week, phase, or research
   activity. Chronology is the researcher's experience of the work, not the reader's need.

## Output

```
1. Answer            one sentence, the verdict
2. Recommendation    what to do, owner by role, by when, confidence
3. Arguments         3-5 sections, each a claim heading + evidence + strength
4. What would have changed this   the disconfirming test and its result
5. Open questions    what this study did not settle, and the next study
6. Method and sample  who, how many, how recruited, segment mix and its skew
7. Limitations       the claims this study cannot support
8. Appendix          raw material, links into `~~research repository`
```

Segment every count against the segment table in the product context, and state the sample's
geography wherever the named sampling trap could apply.

## Quality bar

- The first line is a single falsifiable sentence, not a summary of activity.
- The recommendation names a role and a date.
- There are between three and five arguments, and each heading is a claim, not a topic.
- Reading only the headings reconstructs the answer.
- Method appears after the arguments, never before.
- Every count is stated with its segment and its denominator.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now, before the draft leaves your hands. It is a mandatory gate: a
  report nobody can act on is worse than no report.
- Invoke `research-shareout` next unless the user redirects, to turn the report into a decision
  meeting.
- If a recommendation is accepted and owned, invoke `insight-to-spec`; if accepted but unowned,
  invoke `opportunity-backlog` instead.
- Invoke `impact-tracker` now once the decision is recorded. It is a mandatory gate: a closed
  study with no logged outcome cannot be valued later.
