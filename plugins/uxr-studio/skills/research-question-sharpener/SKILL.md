---
name: research-question-sharpener
description: >
  This skill should be used when the user asks to "sharpen these research questions", "is
  this a good research question", "rewrite my study questions", "our question is too broad",
  or needs fuzzy questions turned into researchable ones. Produces before/after rewrites with
  the specific defect named for each.
metadata:
  version: "0.1.0"
  stage: "intake"
---

# Research Question Sharpener

A good research question is answerable with evidence you can actually collect, falsifiable, and
free of the solution. Most draft questions fail all three at once. Rewrite rather than critique:
always hand back a replacement question, never a note saying the question needs work.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A brief has questions that read like topics ("onboarding", "trust").
- Questions smuggle a feature in ("would a progress bar help users finish?").
- A survey or guide is being drafted and the questions have not been checked.
- Do not use this when the problem is that nobody knows which decision the study serves. Use
  `research-intake-triage` first. Sharpening a question attached to no decision is polish on
  the wrong object.

## Gather first

1. The draft questions, verbatim.
2. The decision they serve.
3. The population and the timeframe the team cares about.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

Apply these five transformations in order. Name which one you applied for each rewrite.

1. **Strip the solution.** Any question containing a feature, a screen, or a fix is a
   hypothesis test dressed as discovery. Remove the artifact and ask about the underlying
   behavior or barrier. Test: can you answer the question without the feature existing? If not,
   it is not a research question, it is a concept test, and it belongs in `concept-test-design`.
2. **Replace intent with past behavior.** "Do users want X" cannot be answered, because stated
   want does not predict use. Rewrite to ask what they did, when, how many times, and what
   happened next. Ask about the most recent instance, not the typical one, because typical
   answers are reconstructions.
3. **Split compounds.** Any question with "and", "or", or a slash is at least two questions with
   different methods and often different populations. Split before scoping. A single number that
   covers two populations is the same defect. Take that case from the known problem areas in the
   product context, where at least one headline rate bundles two populations with different
   causes, and one question cannot serve both.
4. **Bound the population.** Name the segment from the segment table in the product context, and
   name the exclusion. Unbounded questions produce samples drawn from whoever is easiest to reach,
   which is precisely the sampling trap the product context names.
5. **Bound the timeframe.** Attach a window to both the behavior and the observation. "In the
   last two weeks" and "within the first session" are answerable. "Generally" is not.

Also enforce: no question the evidence cannot settle (do not ask people to predict their own
future behavior, explain their own motivations, or price a product they have not used), and no
question whose only possible answer is yes.

### Before and after

Five worked transformations, kept product-free so the move stays visible. Rebuild each pair
around the real draft questions, drawing any stand-in behavior and segment from the known problem
areas and the segment table in the product context.

- Before: "Do users want a progress bar while a file uploads?"
  After: "In the last two weeks, at what point did users abandon an upload, and what were they
  doing at the moment they left?" (Applied: strip the solution, replace intent with past behavior,
  bound population and timeframe.)
- Before: "How do users feel about onboarding?"
  After: "In their first session, which setup step did new accounts stop at, and what did they do
  next?" (Applied: bound population and timeframe, replace a feeling with an observable act.)
- Before: "Why do users churn?"
  After: "Among accounts that cancelled in the last 60 days, what share stopped because the need
  that brought them ended, and what did the others try before cancelling?" (Applied: split
  compound, bound population and timeframe. Cancellation is not always failure, and the two causes
  must be separated before either is treated as a defect.)
- Before: "Is the checkout confusing and do users trust it?"
  After: Two questions. "At the payment step, what do first-time buyers say each line of the total
  covers?" and "What evidence do they cite when they doubt the total?" (Applied: split compounds.
  Comprehension and trust are distinct failures with distinct fixes.)
- Before: "Would users pay more for the automation add-on?"
  After: "Among trialing accounts that used the automation add-on at least once, what did they do
  immediately after, and did they return to it in the same session?" (Applied: strip the solution,
  replace intent with past behavior. Willingness to pay belongs in `pricing-sensitivity`, not in a
  question about wanting.)

Cap a study at four questions. More than four means the study has not been scoped.

## Output

A table, then a short note:

```
| # | Before | After | Defect fixed | Method this now implies |
```

Then: **Questions cut** (with the reason each was dropped or merged) and **Still unanswerable**
(any question no method can settle, with what to do instead).

## Quality bar

- Every rewritten question names a bounded population and a bounded timeframe.
- No rewritten question contains a feature name, a proposed fix, or a solution word.
- No rewritten question asks for prediction, self-reported motivation, or a yes/no whose only
  polite answer is yes.
- Each rewrite names which transformation was applied.
- The final set has four questions or fewer.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now on the sharpened set. It is a mandatory gate: nothing is
  designed against a question before existing evidence is swept.
- Invoke `research-brief-builder` next unless the user redirects, carrying the rewrites verbatim.
- If the method is not yet settled, invoke `method-selector` before the brief is finalized.
- If the chosen method is moderated qual, invoke `interview-guide-builder`; if it is a survey,
  invoke `survey-builder` instead.
- Stop here if the evidence check shows the question is already answered.
