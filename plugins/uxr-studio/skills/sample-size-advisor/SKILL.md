---
name: sample-size-advisor
description: >
  This skill should be used when the user asks "how many participants do we need", "is 5
  users enough", "what sample size for this survey", "how many interviews", or needs a
  defensible n for a qual or quant study. Produces a per-segment sample recommendation with
  the stopping rule or the effect size it was derived from.
metadata:
  version: "0.1.0"
  stage: "scoping"
---

# Sample Size Advisor

Qual sample size is governed by saturation within a segment, so size per segment and plan to
stop when new sessions stop producing new codes. Quant sample size is governed by the smallest
effect worth acting on, so make the team state that effect before computing anything. A number
produced without one of these two anchors is a guess with a decimal point.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A brief needs an n, or someone proposes an n without saying where it came from.
- A study is running and you need a rule for when to stop.
- A stakeholder asks whether a finding from a small sample can be quoted as a rate.
- Do not use this when the question is who to recruit and how to reach them. Use
  `sampling-plan`, and `screener-builder` for the qualifying criteria.

## Gather first

1. Method, and whether the output is a pattern or a number.
2. The segments that must be reported separately.
3. For quant: the smallest difference that would change the decision, and the baseline rate.
4. Recruiting constraints: incentive budget, panel availability, deadline.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Classify the output first.** Pattern (what exists, how it works, why) or number (how many,
   how much, how different). Patterns size on saturation, numbers on effect. A study needing both
   is a sequence. Route it to `mixed-methods-designer`.
2. **Size qual per segment, then add.** Saturation is reached inside a segment, never across
   one. Five participants in one segment and five in another is two samples of five, not a
   sample of ten, and the total is the sum of what each segment needs. Take the segments to be
   sized separately from the segment table in the product context.
3. **Use these starting points, then adjust for segment count.**
   - Interviews: 6–8 per segment. Add 2 if the behavior is episodic or infrequent.
   - Usability tests: 5 per segment per round, and run more rounds rather than a bigger round.
   - Diary studies: 8–12 per segment, expecting 20–30% attrition, so over-recruit.
   - Contextual inquiry: 4–6 per segment. Sessions are long and yield is high per session.
   - Card sort: 15–30 per segment for a closed sort, 12–15 for open.
   - Tree test: 30+ per segment, because the output is a success rate and is therefore quant.
4. **Write the saturation stopping rule into the plan.** Code after every session. Stop when two
   consecutive sessions in a segment produce no new codes, then run one more to confirm. State a
   floor and ceiling up front, for example 6 and 10 per segment, so budget is bounded.
5. **State the five-user limit out loud.** A usability test with 5 users finds frequent problems
   and says nothing about rates. Never let "3 of 5 struggled" become "60% struggle." If the
   decision needs a rate, the method is wrong: use `quant-usability-metrics`.
6. **For quant, make the team state the effect first.** Ask: what is the smallest difference
   that would change what you do? If the answer is "any difference", the decision is not real
   and no n will help. Get the baseline from `~~data warehouse`, not from memory.
   **Hard stop: if the smallest effect worth acting on is not stated, do not continue to the
   arithmetic.** Ask for it with AskUserQuestion and wait. Never infer an MDE, never substitute a
   conventional effect size, never write "TBD", and never proceed assuming the number will arrive
   later. Every quant n in this skill is downstream of that one figure. The only exception is an
   explicitly unattended run, in which case emit the verdict `Blocked: no stated minimum effect
   worth acting on` and nothing else.
7. **Compute from effect and baseline, and show the inputs.** Give the n for the stated effect
   and for one effect half that size, so the team sees the cost of precision. Tables for
   proportion tests, survey margins of error, and completes-to-invites are in
   `references/sample-math.md`.
8. **Convert completes into invites** using the rates in that reference. Recruit to the invite
   number, never the complete number.
9. **Check the population before the arithmetic.** Read the named sampling trap in the product
   context and test the proposed source against it before computing anything. A frame the trap
   invalidates yields a precise number about the wrong population. For questions that touch
   money, draw from the population that actually pays and say so. A clean n from the wrong
   population beats nothing and loses to a small n from the right one.
10. **Check the clock.** Read the feedback-loop length in the funnel section of the product
    context. Where that loop is longer than the study window, no n makes the study answer the
    question, and the plan must name the outcome that will be measured later instead.

## Output

```
## Output type: pattern | number

## Recommended sample
| Segment | n | Source population | Geography | Why this n |

## Stopping rule (qual) or effect basis (quant)
<saturation rule with floor and ceiling> OR <MDE, baseline, power, confidence, n per arm,
and the n for half the effect>

## Recruit to
<invites needed per segment, with the assumed rate>

## What this sample cannot support
<explicit list: rates from qual, subgroup cuts the n cannot carry, causal claims>
```

## Quality bar

- Sample is stated per segment, with the population and geography for each.
- Qual recommendations carry a saturation rule with a floor and a ceiling.
- Quant recommendations show MDE, baseline, power, and confidence, and a second n at half the
  effect.
- The output names at least one claim the sample cannot support.
- Invite counts are given alongside complete counts.
- No qual n is presented as supporting a percentage.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `sampling-plan` next unless the user redirects, handing it the per-segment counts, then
  `screener-builder` for the criteria.
- If the study is an experiment, invoke `ab-test-design` instead, to confirm power against the
  pre-registered decision rule.
- If the study needs both a pattern and a number, invoke `mixed-methods-designer` instead.
- Invoke `research-brief-builder` to record the n and the claims this sample cannot support.
- Stop here if the recommended n cannot be recruited before the decision date. Say so rather than
  sizing down silently.
