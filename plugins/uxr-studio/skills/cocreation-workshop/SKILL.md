---
name: cocreation-workshop
description: >
  This skill should be used when the user asks to "run a co-creation workshop", "do a
  generative session with users", "run a design workshop with stakeholders", or needs a
  structured group session to surface priorities and constraints. Produces a facilitation plan:
  participant mix, a timed divergent-then-convergent agenda, facilitation moves, and outputs.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Co-creation Workshop

Users cannot design solutions, but they can rank tradeoffs and expose constraints you did not
know existed. Structure the workshop to extract priorities and constraints, not artifacts. The
sketches people produce in these rooms are a means of getting them to argue about what matters.
Whatever comes out is a hypothesis to test, never a validated requirement.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- The solution space is wide open and you need to narrow it with people who live the problem.
- A roadmap debate is stuck on priority and nobody has asked users to trade off. Build the
  example by taking two of the standing problem areas from the product context and asking the
  room which one the next quarter of effort should go to.
- You need constraints surfaced early: what would make a solution unusable in the
  participant's real situation.
- Do not use this when you need to evaluate a specific concept. Use `concept-test-design`. Do
  not use it when you need individual depth without group influence. Use interviews via
  `interview-guide-builder`.

## Gather first

1. The problem framing, written as one sentence the group will react to.
2. Who must be in the room and who must not, and whether users and internal staff will mix.
3. The decision this feeds and who owns it.
4. Session length and format, remote or in person.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Cap at 6 to 8 participants, and never mix users with the people who build the product.**
   Users defer to staff within minutes. If both perspectives are needed, run separate sessions
   and compare. Internal observers watch silently, cameras off, on a separate channel.
2. **Recruit for variance across the segments that disagree,** not for representativeness. One
   participant per named segment beats four of the same kind. State the geography of the mix,
   per the product context sampling trap.
3. **Open with a 10-minute individual pass before any discussion.** Everyone writes silently,
   alone, before anyone speaks. This is the single most effective anchoring defense available
   and it costs nothing.
4. **Sequence divergent then convergent, with a hard boundary between them.** Divergent, roughly
   the first half: silent generation, then round-robin share with no critique allowed, then
   build-on. Convergent, the second half: cluster, then force a ranking, then stress-test the
   top items against constraints. Announce the switch out loud. Groups that blend the two
   converge early on whatever was said first.
5. **Make the convergent activity a forced tradeoff, not a vote.** Give each participant a
   fixed budget of points smaller than the number of options, or ask them to rank, or make them
   choose which two of five they would give up. Unlimited dot voting produces consensus on
   everything and decides nothing.
6. **Run a constraints round as its own activity.** Ask: what would have to be true for this to
   work in your actual week, and what would make you abandon it on day one. Constraints are the
   most reusable output of the whole session because they survive a change of solution.
7. **Use four moves to stop the loudest person setting the outcome.** Write before speak, every
   round, no exceptions. Round-robin on a timer so turn order is mechanical rather than social.
   Call on the quietest person first after each silent pass, by name. When someone restates
   another's idea as their own, attribute it back out loud. If one voice still dominates at the
   midpoint, split into pairs for the convergent half and reconvene only to report.
8. **Never ask the group to design the interface.** Ask what the outcome should be, what it must
   not do, and which of two imperfect options they would accept. Sketches are a discussion
   device, so collect them but do not ship them.
9. **Close by reading back the ranked list and the constraints, and get disagreement on the
   record.** Silence in a workshop is not agreement.
10. **Label every output a hypothesis in the artifact itself,** with the test that would
    confirm it written alongside. A workshop validates nothing: the people in the room are not
    a sample and the setting is not their life. Saying this in the deliverable is the only
    reliable way to stop the output being read as a requirement.

## Output

- **Session brief** — problem framing sentence, decision, participant mix, date.
- **Agenda** — timed, with divergent and convergent halves separated and labelled.
- **Activity instructions** — verbatim for each activity, including the tradeoff mechanic and
  its point budget.
- **Facilitation moves** — the four anti-dominance mechanics and the split-into-pairs fallback.
- **Constraints capture template** — what must be true, what would cause abandonment.
- **Output pack** — ranked priorities with vote distribution, constraints list, verbatim
  quotes, and dissent recorded at the close.
- **Hypothesis table** — each top-ranked item, the test that would confirm it, the owner.
- **Assumptions** — anything inferred from the product context.

## Quality bar

- Users and product staff are not in the same room, or the document justifies why.
- Every generative round starts with silent individual writing.
- The convergent activity forces a tradeoff and cannot resolve to "all of it".
- A constraints round exists as its own timed activity.
- Every output is labelled a hypothesis and paired with the test that would confirm it.
- Dissent at the close is captured, not just consensus.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: a session mixing participants is not
  run until it clears.
- Invoke `incentives-and-consent` next unless the user redirects, then `participant-comms` for
  the invites.
- Invoke `assumption-mapper` next unless the user redirects, on the hypothesis table, to rank
  what to test first.
- Invoke `method-selector` next unless the user redirects, to pick the follow-up study.
- If ranked priorities are not being tested this quarter, invoke `opportunity-backlog` and mark
  every entry untested.
- Stop here if the room produced no forced tradeoff.
