---
name: research-intake-triage
description: >
  This skill should be used when the user asks to "triage a research request", "someone
  asked for a survey", "should we research this", "intake a stakeholder ask", or needs to
  turn a vague request into a scoped study, a pointer to existing evidence, or a decline.
  Produces a triage memo with one of three verdicts and the reasoning behind it.
metadata:
  version: "0.1.0"
  stage: "intake"
---

# Research Intake Triage

Most intake requests name a method when they should name a decision. "Can we run a survey on
pricing?" is not a request, it is a guess at an answer. Refuse to accept a method as the
request. Convert every ask into a decision, then decide whether research is the cheapest way
to move it.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A stakeholder sends an ask in `~~chat`, a ticket in `~~project tracker`, or a hallway
  request, and it arrives as a method ("a survey", "some interviews", "a usability test").
- A quarter is being planned and requests need sorting into run / already known / decline.
- Someone escalates a request as urgent and you need a defensible yes or no fast.
- Do not use this when the decision and the question are already agreed and you just need the
  study written down. Use `research-brief-builder` instead.

## Gather first

1. The literal request, in the requester's own words.
2. The decision it is attached to, and who owns that decision.
3. The date the decision gets made with or without research.
4. What the requester already believes the answer is.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Strip the method.** Restate the request with the proposed method deleted. If nothing
   survives deletion, the requester has no question yet. Say so and ask what changes based on
   the result, rather than designing anything.
2. **Name the decision, and stop if it has no owner.** Write it as an action with an owner, in
   the shape "Product decides whether to move X before Y or after it", filled from the decisions
   research is expected to inform in the product context. Not a topic. Not "understand users".
   **Hard stop: if the decision owner or the decision date is not known, do not continue the
   triage.** Ask for them with AskUserQuestion and wait. Never infer an owner, never write
   "TBD", and never proceed on the assumption that the owner will turn up later. An unowned
   decision is the single most common reason a study runs and changes nothing, and inferring
   past it here defeats the whole skill. The only exception is an explicitly unattended run, in
   which case emit the verdict `Blocked: no decision owner` and nothing else.
3. **Run the reversal test.** Ask: what result would make the owner choose the other option?
   Write both branches explicitly. If the owner cannot describe a result that changes their
   mind, this is a request for evidence to justify a decision already made. Name that out loud
   and offer a smaller, honest alternative such as a readout of what is already known.
4. **Check the clock.** Compare the decision date to the shortest credible study. If the study
   lands after the decision, it is not research, it is a post-mortem. Either move the date or
   decline.
5. **Check the feedback loop.** Some decisions cannot be validated quickly regardless of effort.
   Read the feedback-loop length in the funnel section of the product context before promising a
   read date. Where the loop runs longer than the study, a short study can inform the design but
   cannot confirm the outcome. Say which one you are offering.
6. **Check prior evidence before scoping.** Invoke `prior-evidence-check` rather than noting it
   as a next step. A large share of requests die here, correctly, and that is the cheapest
   possible result.
7. **Pick a verdict.** Exactly one of: Scope it, Already answered, Decline. No "maybe later"
   tier. A deferred study with no date is a decline wearing a costume.
8. **Decline well.** A decline names the reason (no decision, no owner, decided already, answer
   exists, loop too long, wrong instrument), offers the cheapest substitute, and leaves the door
   open with a specific trigger. Draw the trigger from the known problem areas in the product
   context, in the shape "reopen this when <the cut that makes the question answerable> exists in
   `~~data warehouse`." Declining well is part of the job, not a failure of service.
9. **Split compound requests.** Requests often bundle two problems into one number. Use the known
   problem area in the product context that explicitly warns against researching one rate as one
   problem: it is two populations with two causes, so it is two studies, or one study and one
   engineering ticket. Triage them apart.

## Output

```
## Request
<verbatim ask, with requester and date>

## Decision this would change
<action + owner + decision date>

## Reversal test
If we learn <A>, the owner does <X>. If we learn <B>, the owner does <Y>.

## Verdict: Scope it | Already answered | Decline

### If Scope it
Sharpened question · proposed method family · rough sample · time to answer ·
what this will not answer · next skill

### If Already answered
Source(s) in ~~research repository / ~~data warehouse / ~~product analytics /
~~support desk · the answer in two sentences · confidence · what is still open

### If Decline
Reason · cheapest substitute offered · the trigger that reopens it
```

Keep the whole memo under one screen. Triage that takes a day is not triage.

## Quality bar

- The verdict is one of the three, stated before any detail.
- The decision is written as an action with a named owner and a date.
- Both branches of the reversal test are filled in with plausible findings, not placeholders.
- No method name appears before the decision is stated.
- Compound requests are split, and any two-population number is flagged as two studies.
- A decline includes a substitute and a reopen trigger.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now on a "Scope it" verdict. It is a mandatory gate: a study
  scoped without it may already have an answer.
- If the sharpened question is still fuzzy after triage, invoke `research-question-sharpener`
  before the evidence check.
- Invoke `research-brief-builder` next once the evidence check clears, then register the study
  in `research-roadmap`.
- If the request split into two studies, run this skill again on the second before designing
  either.
- Stop here on a "Decline" or "Already answered" verdict. Invoke nothing further.
