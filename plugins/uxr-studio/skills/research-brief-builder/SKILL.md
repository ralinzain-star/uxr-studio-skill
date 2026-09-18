---
name: research-brief-builder
description: >
  This skill should be used when the user asks to "write a research brief", "scope this
  study", "draft a one-pager for this research", "kick off a study", or needs a signed
  agreement on what a study will and will not answer before fieldwork starts. Produces a
  one-page brief with a decision, an owner, a date, and an explicit out-of-scope list.
metadata:
  version: "0.1.0"
  stage: "intake"
---

# Research Brief Builder

The brief is a contract, not a plan document. Its single most important line is "this study is
worth running only if the answer could change X". Write that line first. If it cannot be
written, stop and go back to triage rather than writing a longer brief.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A request has passed triage and needs to be written down before anyone recruits or designs.
- A study is already running without a brief and keeps changing shape. Write the brief late
  rather than never, and freeze it.
- A stakeholder needs to approve scope, budget, or timeline.
- Do not use this when the request is still a method or a topic rather than a decision. Use
  `research-intake-triage` first.

## Gather first

1. The decision, its owner, and the date it gets made.
2. What the team already believes and how strongly.
3. Constraints: deadline, budget, incentive ceiling, who must be recruited.
4. Anything already known from `prior-evidence-check`.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the worth-running line first.** Format: "This study is worth running only if the
   answer could change <specific decision>." One decision. A brief that lists three decisions
   is three briefs, and it will satisfy none of them.
2. **Name the decision owner as a person, not a team.** "Product" does not sign off. A named
   owner is the person who will be in the room when the result lands.
   **Hard stop: if the named decision owner or the decision date is not known, do not continue
   the brief.** Ask for them with AskUserQuestion and wait. Never infer them, never write "TBD",
   and never proceed assuming they will arrive later. The quality bar requires a real person and
   a real date, and a brief that names neither is a document nobody is held to. The only
   exception is an explicitly unattended run, in which case emit the verdict `Blocked: no named
   decision owner or decision date` and nothing else.
3. **Set the decision date before the study date.** Work backwards. The study must deliver at
   least one week before the decision, so there is time to be wrong about the schedule.
4. **State current belief with a confidence number.** Force the team to write "we believe 70%
   that users abandon after the first scan because the score is not actionable." Vague belief
   produces vague findings, because nobody can tell afterwards whether anything was learned.
   Record the belief per stakeholder if they disagree; disagreement is the most valuable thing
   on the page.
5. **Write the change-our-mind condition.** For each belief, the specific finding that would
   overturn it. If a belief has no overturning condition, delete it from the brief and mark it
   as a constraint instead, because it is not being tested.
6. **Choose method last.** Method follows from the question, the population, and the clock, in
   that order. Delegate the choice to `method-selector` and paste the result.
7. **Specify the sample by segment, not by count alone.** Name the segment from the product
   context, the source, and the geography. Example: 8 trialing users who ran at least two scans,
   recruited from the trialing population rather than site traffic, US and Canada, because
   traffic-drawn samples over-represent a population that does not convert.
8. **Write the NOT-answering list.** Three to five lines. This is the section that prevents the
   readout being judged against a question nobody asked. Include anything the method cannot
   support: rates from a small qual sample, causality from a correlational survey, renewal
   behavior from a two-week study against a ~90-day loop.
9. **Freeze it.** Scope changes after sign-off get a new version number and a one-line note on
   what moved and who approved it.

## Output

A single page, in this order:

```
# <Study name>

**Worth running only if:** the answer could change <decision>.

| Field | Value |
|---|---|
| Decision | <action> |
| Decision owner | <person> |
| Decision date | <date> |
| Study delivers by | <date, at least 1 week earlier> |

## What we believe now
| Belief | Who holds it | Confidence | What would change our mind |

## Research questions
<2-4, from research-question-sharpener>

## Method
<method + why this one, one sentence>

## Sample
<n per segment · source · geography · screening criteria in one line>

## Timeline
<recruit / field / synthesize / share, with dates>

## This study will NOT answer
<3-5 bullets>

## Ethics and participant care
<incentive, consent, anything the product context forbids asking>
```

## Quality bar

- The worth-running line names exactly one decision and appears first.
- The decision owner is a named person and the decision date is a real date.
- Every belief has a confidence figure and an overturning condition.
- The sample names segment, source, and geography, not just a number.
- The NOT-answering list has at least three entries and includes the method's real limits.
- The whole brief fits on one page.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If `prior-evidence-check` has not run on this question, invoke it now. It is a mandatory gate:
  no study is designed before existing evidence is swept.
- If any research question still names a solution or lacks a bounded population, invoke
  `research-question-sharpener` instead.
- Invoke `method-selector` next unless the user redirects, then `sample-size-advisor` and
  `sampling-plan` for the sample line.
- If a belief has no overturning condition, invoke `assumption-mapper`.
- Invoke `research-roadmap` to register the study against its decision date.
