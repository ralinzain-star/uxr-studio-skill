---
name: mixed-methods-designer
description: >
  This skill should be used when the user asks to "design a mixed methods study", "combine
  qual and quant", "we have the numbers but not the why", "survey plus interviews", or needs
  two strands of evidence designed to meet rather than run in parallel. Produces a sequenced
  design with a named integration point and a decision rule for each strand.
metadata:
  version: "0.1.0"
  stage: "scoping"
---

# Mixed Methods Designer

Sequence beats simultaneity. Choose the order deliberately: quant then qual to explain a number
you already have, qual then quant to size a pattern you just found. Run them at the same time
only when they answer different questions. Name the integration point before fieldwork starts,
because a study without one produces two reports, not one.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A metric moved and nobody can explain it.
- Interviews surfaced a pattern and someone asks how common it is.
- A decision needs both a rate and a mechanism, and one strand alone will be dismissed.
- Do not use this when one method answers the question. Use `method-selector`. A second strand
  added for credibility rather than need doubles cost and halves depth.

## Gather first

1. The decision and the questions, ideally already through `research-question-sharpener`.
2. What is already measured, and where it lives.
3. The decision date and the total time available.
4. Whether the population for each strand is the same people or different people.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Pick the sequence from the starting condition, not from preference.**
   - **Quant then qual (explanatory).** Use when a number exists and is not understood. The
     number defines the sample. Draw the worked example from the known problem areas in the
     product context: where a headline rate there bundles two populations, pull the cut from
     `~~data warehouse` first and interview inside one slice only, never the blended number,
     because the other slice is a different problem with a different fix.
   - **Qual then quant (generative).** Use when a pattern was discovered and needs sizing. The
     qual supplies the answer options, the vocabulary, and the segment definitions for the
     survey or the query. Never write a survey before the qual that generates its options, or
     the response options will be the team's guesses and the data will confirm them.
   - **Simultaneous.** Only when the strands answer genuinely different questions on the same
     decision, for example a usability test on comprehension running alongside a warehouse pull
     on adoption rates. State the two questions separately. If you cannot, the design is really
     sequential and you have not chosen the order.
2. **Write the integration point as a sentence with a mechanism.** Format: "Strand A produces
   <artifact>, which is used to <do something specific> in strand B." Examples: "The warehouse
   cohort of users who cancelled within 7 days of trial start becomes the recruiting list for
   the interviews." "The eight codes from thematic coding become the closed response options in
   the survey question on cancellation reason." Vague integration ("we will triangulate") is the
   failure this skill exists to prevent.
3. **Give each strand its own decision rule.** Write what result from strand A changes strand
   B's design, including the result that cancels strand B. A second strand you would run
   regardless of the first is not sequenced, it is two studies.
4. **Set the handoff date.** Strand A must deliver early enough that strand B can be built from
   it. Budget at least one week between strands for synthesis. Compressing this is how teams run
   strand B on the design they drafted before strand A started.
5. **Check the loop length.** Read the feedback-loop length in the funnel section of the product
   context before promising a read date. Two sequential strands against a loop longer than the
   study window will not validate the downstream behavior. Say which part of the answer is
   evidence and which is inference.
6. **Keep the samples separable.** If the same people appear in both strands, treat the second
   exposure as a limitation. If they differ, state what makes the populations comparable and give
   the geography for both. Check both frames against the sampling trap named in the product
   context, since a conveniently drawn sample will not match a warehouse cohort.
7. **Decide in advance what disagreement means.** If the qual mechanism does not appear in the
   quant data, which strand wins and why. Teams that skip this default to whichever strand
   matches the pre-existing belief.

## Output

```
## Decision and questions

## Sequence: quant-then-qual | qual-then-quant | simultaneous
Why this order, one sentence.

## Strand A
Question · method · population and source · n · dates · what it produces

## Integration point
Strand A produces <artifact>, used to <mechanism> in strand B.

## Strand B
Question · method · population and source · n · dates · built from <artifact>

## Decision rules
If A shows <X>, B becomes <Y>. If A shows <Z>, B is cancelled.
If A and B disagree, <rule>.

## One report, not two
The single claim the combined study will support.

## Limits
What neither strand can answer.
```

## Quality bar

- The sequence is named and justified by the starting condition.
- The integration point names a concrete artifact and a concrete mechanism.
- Strand B's design is written as dependent on strand A's output, including a cancel condition.
- Each strand states population, source, geography, and n.
- A disagreement rule exists and was written before fieldwork.
- The output names one claim the combined study supports, not a list per strand.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now unless it has already run. It is a mandatory gate: a
  two-strand study is the most expensive way to learn something already known.
- Invoke `sample-size-advisor` next unless the user redirects, then `sampling-plan` for each
  strand.
- Invoke `interview-guide-builder` or `survey-builder` for strand A only; strand B is built
  after the integration point delivers.
- Stop here until strand A reads out. Then invoke `triangulation`, and `pyramid-report` for the
  single combined claim.
