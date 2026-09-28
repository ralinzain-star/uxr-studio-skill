---
name: insight-to-spec
description: >
  This skill should be used when the user asks to "turn this insight into a ticket", "hand this
  to the product team", "write the problem statement for engineering", or needs a finding to
  become buildable work. Produces a problem-and-evidence handoff with a measurable acceptance
  criterion and no proposed solution.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# Insight To Spec

Hand over the problem, the evidence, and the success criterion. Never the solution. A researcher
who writes the solution loses the argument about whether the problem is real, because the room
stops evaluating the evidence and starts critiquing the design. Keep the problem indivisible from
its evidence and let the team own the fix.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A finding has been accepted and a team is ready to work on it.
- A ticket exists that cites research but has lost the evidence trail.
- A team asked "so what do you want us to build" and the honest answer is a problem, not a build.
- Do not use this when no team has capacity and no owner exists. Park it in
  `opportunity-backlog` until one does.

## Gather first

1. The insight in final wording, and its evidence strength after `so-what-checker`.
2. The team that will own it and the surface it touches, named from the features section of the
   product context.
3. The current measured baseline for whatever the fix is supposed to move.
4. The decision or roadmap slot this feeds, from the decisions section of the product context.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the problem statement as a behaviour under a condition.** Shape: "When <condition>,
   <segment> <does or fails to do> <behaviour>, because <mechanism>, which costs <consequence>."
   If the mechanism is unknown, write "mechanism unknown" rather than inventing one. A problem
   statement with a guessed mechanism is a solution in disguise.
2. **Size it.** How many users hit this condition, how often, and in which segment. Pull the
   denominator from `~~product analytics` or `~~data warehouse`. An unsized problem loses every
   prioritisation argument to a sized one, however trivial.
3. **Write one acceptance criterion and make it measurable.** It must name a metric that already
   exists or can be instrumented before the work starts, a direction, a magnitude, a population,
   and a window. Reject criteria of the form "users find it easier" or "improved clarity". Check
   the feedback-loop length in the product context before choosing the window, because a criterion
   that cannot resolve inside the team's planning horizon will be quietly dropped.
4. **Attach the evidence trail as links, not as a summary.** Session clips or transcript
   timestamps in `~~research repository`, the query or dashboard in `~~product analytics`, the
   report section, and the sample composition including its geography and any sampling trap that
   applies. The trail is what survives the team's reorganisation.
5. **State the constraints that are findings, not preferences.** Anything the evidence shows a
   fix must not do. These are legitimate to specify because they came from users. Include the
   relevant participant-facing language and tone rules from the product context where a fix
   touches user-visible copy.
6. **Offer solution space, never a solution.** If the team wants direction, give two or three
   distinct directions with the trade-off each makes, and say explicitly that you are not
   recommending among them and will happily test whichever they pick.
7. **Negotiate when a team jumps to a fix that does not address the finding.** Do not say no. Ask
   the team to state which part of the problem statement the fix changes, and which acceptance
   criterion it moves. When it moves none, that is visible without an argument. Then offer the
   trade: ship it if they want, but instrument the acceptance criterion anyway, and agree in
   advance what result would mean the real problem is still open. Escalate only when a fix would
   make the measured criterion look better while the behaviour gets worse. Say that plainly and
   propose the guardrail metric.
8. **File it where the team works,** in `~~project tracker`, and link back to
   `~~research repository` from the ticket so the trail is two-way.

## Output

```
Title             the problem, in the team's language
Problem statement condition / segment / behaviour / mechanism / consequence
Size              users affected, frequency, segment, source and denominator
Acceptance criterion  metric, direction, magnitude, population, window, instrumentation status
Constraints from evidence   what a fix must not do, and why
Solution space    2-3 directions with trade-offs, explicitly not a recommendation
Evidence trail    links to clips, transcripts, queries, report section, sample composition
Owner and slot    team, role accountable, roadmap slot, review date
Open question     what remains unknown and what would settle it
```

## Quality bar

- No solution is specified anywhere outside the clearly labelled solution space.
- The problem statement includes a condition and a consequence, and flags an unknown mechanism.
- The acceptance criterion names metric, direction, magnitude, population, and window.
- The criterion's window fits the product's feedback-loop length.
- Every evidence claim links to a source rather than restating it.
- The sample's composition and any applicable sampling trap are stated.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `decision-metrics-mapper` next unless the user redirects, so the acceptance criterion
  is tied to a metric the company already tracks.
- Invoke `impact-tracker` now to log the handoff. It is a mandatory gate: an unlogged handoff
  cannot be read back when someone asks what research changed.
- If no team owns the ticket, invoke `opportunity-backlog` instead and link the entry to the
  evidence trail.
- Stop here once the ticket is filed, the criterion is instrumented, and the handoff is logged.
