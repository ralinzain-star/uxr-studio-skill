---
name: research-roadmap
description: >
  This skill should be used when the user asks to "plan the research roadmap", "what should
  we research this quarter", "sequence our studies", "build a research plan for H2", or needs
  studies ordered across a quarter or half. Produces a decision-dated sequence with declared
  slack and an explicit not-doing list.
metadata:
  version: "0.1.0"
  stage: "scoping"
---

# Research Roadmap

Sequence studies by decision date, not by topic tidiness. A roadmap grouped into themes looks
coherent and delivers late; a roadmap ordered by when each answer is needed looks messy and
lands. Leave deliberate slack for the reactive request that will arrive, because it always does
and an unplanned roadmap absorbs it by silently dropping the last planned study.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A quarter or half is being planned and multiple studies are competing for the same weeks.
- A roadmap exists but keeps slipping, and you need to find where the sequencing is wrong.
- Headcount or budget is fixed and someone must be told what will not happen.
- Do not use this when a single request needs a yes or no. Use `research-intake-triage`.

## Gather first

1. The candidate studies, each with its decision and decision date.
2. Researcher capacity in weeks, and any fixed commitments already on the calendar.
3. Company milestones that create hard dates: launches, pricing changes, planning cycles.
4. The recruiting lead time for each population.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Reject any candidate without a decision date.** Send it back to `research-intake-triage`.
   Undated studies fill slack and then justify themselves after the fact.
2. **Work backwards from each decision date.** Deliver one week before the decision. Add the
   study's own duration, then add recruiting lead time in front of it, then add one week of
   synthesis before delivery. The result is the latest possible start. Order by latest start,
   ascending. That ordering is the roadmap.
3. **Account for feedback-loop length separately from study length.** Some questions cannot be
   confirmed inside the quarter no matter how the weeks are arranged. Read the feedback-loop
   length in the funnel section of the product context and compare it to the horizon. Where a
   question sits downstream of a loop longer than the plan, a sprint-length study informs the
   design and cannot validate the outcome. Mark such studies "informs, does not validate" and
   schedule the validation read for the following quarter as its own line item.
   This is the single most common planning error: teams book the study and forget the read.
4. **Cap concurrency at two studies in fieldwork per researcher, one if either is moderated.**
   Synthesis is the bottleneck, not sessions. Overlapping fieldwork produces backlogged
   transcripts, and transcripts that age lose their findings.
5. **Reserve 20% of capacity as named slack.** Put it on the roadmap as a block with a label
   such as "reactive intake, weeks 5–6", not as unallocated whitespace. Unnamed slack gets
   booked by the loudest request in week two. Review the block at each month boundary and let
   unused slack absorb overruns rather than pulling work forward.
6. **Front-load the cheap disconfirming work.** Put `prior-evidence-check` in front of every
   study and give it half a week of its own. Studies die here, freeing weeks. A roadmap that
   assumes no study will be cancelled is a forecast, not a plan.
7. **Sequence dependent studies with the handoff visible.** If study B needs study A's output,
   draw the dependency and the artifact that crosses it. Use `mixed-methods-designer` for the
   design. If A slips, B slips; state that on the roadmap rather than discovering it later.
8. **Stagger recruiting load by population.** Two studies that both need the same scarce segment
   from the product context in the same fortnight will compete for the same list and burn goodwill
   with the same people. Space repeated draws on a scarce population by at least four weeks.
9. **Write the not-doing list.** Name the candidates that did not fit, with the reason and the
   quarter they are eligible for. A roadmap without a not-doing list is a wish list, and every
   stakeholder will assume their request is on it.

## Output

```
# Research roadmap — <quarter or half>

## Capacity
<researcher weeks available · committed · slack reserved>

## Sequence
| Study | Decision it serves | Owner | Decision date | Latest start | Duration | Population | Dependency |
<ordered by latest start>

## Timeline
<week-by-week grid, one row per study, with the named slack block shown>

## Informs but does not validate
<studies whose feedback loop exceeds the horizon, with the follow-up read scheduled>

## Not doing this period
| Candidate | Why | Eligible from |

## Assumptions this plan rests on
<recruiting rates, capacity, fixed dates>
```

## Quality bar

- Every study on the roadmap has a decision, an owner, and a decision date.
- The sequence is ordered by latest start derived from the decision date, not by topic.
- Slack is a named, dated block of at least 20% of capacity.
- Long-loop studies are marked "informs, does not validate" with a follow-up read scheduled.
- Concurrency never exceeds two studies in fieldwork per researcher.
- The not-doing list is present and each entry has a reason and an eligibility date.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now on the first study in the sequence. It is a mandatory gate:
  nothing on this roadmap is designed before existing evidence is swept.
- Invoke `research-brief-builder` next unless the user redirects, once per accepted line in
  sequence order.
- If a candidate lacks a decision owner or a decision date, invoke `research-intake-triage` on it
  instead.
- Invoke `impact-tracker` to open an entry per scheduled study.
- Stop here once the period is sequenced; re-review it with `research-process-audit` at period
  end.
