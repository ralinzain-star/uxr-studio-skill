---
name: method-selector
description: >
  This skill should be used when the user asks "what method should we use",
  "how should we research this", "should this be a survey or interviews",
  "is this qual or quant", or has a research question and no study design yet.
  Produces a recommended method set with a reason, a rejected-alternatives note,
  and the sequence in which the methods run.
metadata:
  version: "0.1.0"
  stage: "scoping"
---

# Method Selector

Pick the method from the shape of the question, never from preference, availability, or what
the team ran last time. Most method arguments are actually unresolved arguments about the
question, so if the method is contested, the question is not sharp enough yet.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A research question exists but no study design does.
- Someone has proposed a method and you need to check whether it answers the question.
- Two methods are being argued for and the argument is going in circles.
- Do not use this when the question itself is still fuzzy. Run `research-question-sharpener`
  first. A method chosen for a vague question will be defensible and useless.

## Gather first

1. The decision this feeds, and the date it gets made.
2. Whether the question asks **what is happening**, **why it is happening**, **how many / how
   much**, or **what would happen if** — this single classification drives most of the answer.
3. What is already known, from `prior-evidence-check` or from the standing problem areas in the
   product context.
4. The hard constraints: time until the decision, access to the population, whether the thing
   being studied exists yet.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

**Step 1. Classify the question into one of four types.** Do this explicitly and say which one
you chose, because everything downstream depends on it.

| Question type | It sounds like | Evidence it needs |
|---|---|---|
| Descriptive | what are people doing, where do they drop, how common is this | Behavioral data, then survey for prevalence |
| Explanatory | why does this happen, what is going on here | Qualitative, always. No survey answers a why. |
| Evaluative | does this work, can people do it, is it better | Task-based testing, or an experiment |
| Predictive | what will happen if we change this, what will they choose | Experiment for live changes, forced-tradeoff for unbuilt ones |

**Step 2. Apply the elimination rules before choosing anything.** These kill more bad designs
than any positive recommendation.

- A survey cannot tell you why. Self-reported causes are rationalizations. If the question has
  a why in it, the survey is at best a sampling instrument for the interviews.
- A small qualitative study cannot tell you how many. If the question has a number in it, you
  need `quant-usability-metrics`, `survey-builder`, or behavioral data.
- Nobody can reliably predict their own future behavior. Questions about what users *would* do
  become either a forced tradeoff (`maxdiff-and-tradeoff`, `concept-test-design`) or a live
  experiment (`ab-test-design`).
- Analytics locate, they never explain. A funnel gives you where, and the explanation is always
  a second study.
- If the thing does not exist yet, evaluative methods are unavailable. Drop to concept testing
  and be honest that you are measuring comprehension and stated tradeoff, not usage.

**Step 3. Check whether this needs a study at all.** If `prior-evidence-check` has not run,
run it. The cheapest correct answer is that the evidence already exists.

**Step 4. Choose the primary method, then ask what it cannot see.** Every method has a blind
spot, and the second method exists to cover it, not to add volume. If the second method has no
distinct job, do not run it. Where two are genuinely needed, hand off to
`mixed-methods-designer` to fix the sequence and the integration point.

**Step 5. Constrain by the decision date, not by ambition.** Work backwards from the date. If
the full method does not fit, do not silently shrink the sample: either pick a cheaper method
that honestly answers a smaller question, or run `lightning-synthesis` on a reduced scope and
label the confidence. Check the feedback-loop length in the funnel section of the product
context. If the outcome you care about cannot be observed before the decision, say so and pick
a leading indicator instead of pretending.

**Step 6. Write down what you rejected and why.** This is the part that stops the argument from
restarting in two weeks, and it is the part everyone skips.

## Output

```
Question type: <descriptive | explanatory | evaluative | predictive>
Restated question: <one sentence, in the sharpened form>

Recommended: <method> via `<skill-name>`
  Because: <one sentence tying the method to the question type>
  Blind spot: <what this method cannot see>
  Covered by: <second method and skill, or "accepted, not covered, because ...">

Sequence: <ordered, with what each step hands the next>
Sample and effort: <n, segments, rough elapsed time>
Reads by: <date, relative to the decision date>

Rejected:
  <method> — <the specific reason it does not answer this question>
  <method> — <reason>

Confidence this design answers the question: <high | medium | low>, because <reason>
```

## Quality bar

- The question type is named explicitly and the recommendation follows from it.
- At least two alternatives are rejected by name, each with a reason specific to this question
  rather than a general property of the method.
- No method is recommended that structurally cannot produce the required evidence, especially
  a survey for a why or a small qualitative sample for a rate.
- The design reads before the decision date, or the skill says plainly that it cannot and
  offers the honest alternative.
- Every method named maps to a skill that exists in this plugin.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now unless it has already run. It is a mandatory gate: no study
  is designed before the evidence is swept, and a Kill verdict ends the chain.
- Invoke `research-brief-builder` next unless the user redirects, then `sample-size-advisor`
  and `sampling-plan`.
- If the design has two strands, invoke `mixed-methods-designer` first to fix the sequence and
  the integration point.
- If the question turned out to be a funnel drop, invoke `workflow-conversion-diagnosis`
  instead; it runs the chain step by step without pausing between steps.
