---
name: prior-evidence-check
description: >
  This skill should be used when the user asks "do we already know this", "check prior
  research", "has anyone studied this before", "what does the data already say", or needs
  existing evidence swept before a study is designed. Produces a short memo splitting what is
  known, what is genuinely unknown, and what is contested, with a kill-or-sharpen verdict.
metadata:
  version: "0.1.0"
  stage: "scoping"
---

# Prior Evidence Check

Run this before designing anything. It is skipped constantly and it is the cheapest study you
will ever run: a half day of searching regularly kills a six-week study or shrinks it to a
single question. Treat "we should check first" as a step with a deliverable, not a good
intention.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study has been accepted in triage and has not yet been designed.
- Someone asserts a fact about users and nobody can name the source.
- A recurring question comes back for the third time and the answer may already exist.
- Do not use this when the goal is to synthesize several existing studies into one position on
  a question. Use `triangulation`.

## Gather first

1. The question, ideally already through `research-question-sharpener`.
2. The population and timeframe it concerns.
3. Which sources you have access to and which need a request.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

Walk the sources in this fixed order. The order runs cheapest and most conclusive first, and
stopping early is a legitimate outcome at any step.

1. **`~~research repository`.** Search by behavior and by segment, not by feature name, because
   the feature was probably called something else. Check for past studies, raw transcripts, and
   insights. Note the date on everything: a finding about first-session behavior from before a
   redesign is a historical fact, not a current one. Set a staleness line, usually 12 months,
   and mark everything older as needing confirmation.
2. **`~~data warehouse`.** Governed metrics answer more questions than teams expect. Pull the
   rate, the trend, and the segment cut. Check whether the number the team is quoting is the
   governed one. For the worked example, take a known problem area from the product context whose
   headline rate bundles two populations: cutting it properly dissolves one number into two rates
   with two different causes, and that cut alone often replaces the proposed study with an
   engineering ticket.
3. **`~~product analytics`.** Funnel steps, feature adoption, and session recordings. Recordings
   are the closest thing to free qual: twenty minutes of watching first sessions answers many
   comprehension questions that were about to be interviewed for. Note the population the tool
   is reporting on, since traffic-level analytics over-represent whoever the sampling trap named
   in the product context describes.
4. **`~~support desk`.** Tickets, cancellation reasons, and refund requests carry verbatim
   language and frequency together. Read the last 60 days rather than a sample. Cancellation
   reason codes are self-selected and unreliable as rates, but their free-text is excellent
   vocabulary for a later survey.
5. **`~~review sites`.** Public reviews and app-store feedback. Biased toward extremes, useful
   for finding failure modes you had not named, never useful for prevalence. Read for the
   unfamiliar complaint, not the common one.

Then:

6. **Sort every finding into one of three buckets: known, unknown, contested.** A finding is
   known only if it names a source, a date, a population, and a method. Everything else is
   contested or unknown. Do not let a widely repeated belief enter "known" without a source.
7. **Treat contested as the most valuable bucket.** Two sources disagreeing usually means they
   measured different populations or different timeframes. Reconcile before designing, because
   a study built on the wrong side of a contested number answers nothing.
8. **Issue a verdict.** Kill (the answer exists, write it up and stop), Sharpen (most is known,
   the study shrinks to the residual unknown), or Proceed (genuinely unknown, design at full
   size). Sharpen is the most common outcome. Say how many weeks the check saved.

## Output

```
# Prior evidence: <question>

## Verdict: Kill | Sharpen | Proceed
<one sentence, plus weeks saved or residual scope>

## What we already know
| Claim | Source | Date | Population | Method | Confidence |

## What is genuinely unknown
<bulleted, each phrased as a researchable question>

## What is contested
| Claim A | Claim B | Likely reason they differ | How to reconcile |

## Sources checked and what was not available
<list all five, including any that returned nothing, and any access still needed>
```

Keep it to one page. If it takes longer than half a day, stop and report what you have.

## Quality bar

- All five sources are listed, including those that returned nothing.
- Every "known" claim carries source, date, population, and method.
- Anything older than the stated staleness line is marked as needing confirmation.
- The unknown bucket is written as researchable questions, not topics.
- The contested bucket names why the sources differ, not just that they do.
- The verdict is one of the three and states residual scope or weeks saved.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If the verdict is Kill, invoke `insight-writer` to write the existing answer down once, then
  stop. Design nothing.
- If the verdict is Sharpen, invoke `research-question-sharpener` on the residual unknowns,
  then `research-brief-builder`.
- If the verdict is Proceed, invoke `research-brief-builder` next, then `method-selector` if no
  method has been chosen yet.
- Invoke `research-repository-hygiene` next so this memo is findable when the question returns.
