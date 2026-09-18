---
name: concept-test-design
description: >
  This skill should be used when the user asks to "test a concept", "validate an idea before
  we build it", "get reactions to this pitch", "will users want this feature", or needs to
  evaluate an unbuilt product idea. Produces a concept test design with stimulus, a
  comprehension check, a forced tradeoff, and rules for reading the result.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Concept Test Design

Concepts get false praise. In a research session people are polite, imaginative, and free of
consequences, so enthusiasm on its own is worthless. Every concept test must attach a cost: a
commitment, a tradeoff against something they already have, or a rank against real
alternatives. Without a cost you measured politeness.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- An idea is competing for roadmap space and nothing is built yet.
- Two or three directions need separating before design invests in one.
- A pitch or positioning line needs a reaction from the target segment.
- Do not use this when the thing exists and the question is whether people can operate it, use
  `usability-test-plan`. For attribute weight at scale use `maxdiff-and-tradeoff`, and for
  willingness to pay use `pricing-sensitivity`.

## Gather first

1. The decision that follows a yes and the decision that follows a no.
2. The concepts, in one sentence each, and whether they compete or stack.
3. The segment, and what those people use today to get the same outcome.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write each concept as a one-sentence value claim before any stimulus exists.** Format:
   "For <segment> who <situation>, this <does what> so that <outcome>." If the claim needs a
   paragraph, the concept is two concepts. Split it.
2. **Build stimulus concrete enough to react to, vague enough not to prototype.** One card per
   concept: a headline, two or three plain sentences, and one realistic artifact showing the
   output. Never a clickable flow, which shifts the session to usability and costs a week you
   have not spent. No branding, no pricing, no superlatives.
3. **Run the comprehension check before the reaction, always.** "In your own words, what does
   this do, and who is it for?" Record it verbatim before saying anything else. A concept four
   of six people restate wrong has already failed, and their enthusiasm is for the thing they
   imagined. Fix it and rerun. Common mistake: correcting them and continuing, which invalidates
   everything after.
4. **Then take the unaided reaction, once, and move on.** "What is your first reaction?" and
   "who is this not for?" Two minutes. Do not chase warmth.
5. **Impose the cost. Pick one mechanism and run it the same way for every participant.**
   - **Commitment.** Ask for something that costs them: join a waitlist now, give a date they
     would try it, agree to a 15-minute follow-up when it exists. Record who actually does it,
     not who says they would.
   - **Tradeoff against the current solution.** Quote what they already do, in their own words
     from earlier in the session, and ask what they would give up. Phrase it as: "You said you
     <the workaround they just described>. Would this replace that, sit next to it, or would you
     skip it?" Skipping is a legitimate answer. Make it easy to pick.
   - **Forced rank with constrained budget.** Put all concepts plus their status quo on the
     table, make them rank, then give them one pick to actually receive. The last cut and the
     one pick carry the signal. A rank with no scarcity is another opinion.
6. **Anchor against the real alternative, including doing nothing.** Every concept competes
   with the current workaround and with ignoring the problem. If "keep doing what I do" is not
   on the table, the test is rigged and the result will not survive launch.
7. **Read enthusiasm as the weak signal it is.** Behavior taken in session beats a stated
   tradeoff, which beats a rank, which beats a rating, which beats praise. Report praise as
   context, never as a finding. Discount any concept with unanimous support and three different
   comprehension answers. Flag samples exposed to the geography trap in the product context:
   low-monetizing traffic enthuses freely.
8. **Recruit for consequence.** For monetized concepts recruit trialing or paying users, never
   raw traffic. The low-urgency segment in the product context's segment table is useful for
   direction and useless for demand.

## Output

```
# <Concept set>: Concept Test Design
Decision on yes · decision on no · segment · n · recruit source

## Concepts
C1 value claim (one sentence) · stimulus description · what it must be believed to do

## Session flow
Context questions (the current workaround, in their words) · comprehension check ·
unaided reaction · cost mechanism · anchor against status quo · close

## Cost mechanism
<which one, the exact wording, and what counts as a yes>

## Reading rules
Evidence ranked · kill threshold · what this test cannot tell you
```

## Quality bar

- Every concept has a one-sentence value claim and no superlatives in its stimulus.
- The comprehension check runs before any reaction question, with a stated fail threshold.
- Exactly one cost mechanism is specified, with the wording and the yes criterion written out.
- The status quo is a selectable option everywhere a choice is made.
- The plan says in one line that enthusiasm is not evidence, and names the kill condition.
- The recruit source matches the decision: paying or trialing users for monetized concepts.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: the stimulus and cost mechanism are not
  shown to participants until it clears.
- Invoke `incentives-and-consent` next unless the user redirects, then `participant-comms` to
  recruit paying or trialing users.
- Invoke `interview-moderation` next unless the user redirects, and `session-debrief` after
  every session.
- If a concept clears the kill threshold and something is clickable, invoke
  `usability-test-plan` instead of retesting the concept.
- If the open question is willingness to pay, invoke `pricing-sensitivity`.
- Stop here if every concept failed the comprehension check; rewrite the stimulus.
