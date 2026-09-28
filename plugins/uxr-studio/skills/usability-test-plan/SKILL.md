---
name: usability-test-plan
description: >
  This skill should be used when the user asks to "plan a usability test", "write test
  tasks", "test this prototype with users", "is this flow usable", or needs a moderated
  task-based evaluation of an interface. Produces a test plan with tasks written as goals,
  per-task measures, and a moderator protocol.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Usability Test Plan

Test tasks, not features. Write every task as a goal with a realistic trigger and zero product
vocabulary in it, because a task that names the button is testing reading comprehension, not
usability. A small-n usability test finds problems. It does not estimate how often they happen.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A flow, prototype, or shipped screen needs evaluating.
- A team disagrees about whether something is confusing and wants evidence in a week.
- A redesign needs a baseline of where people currently get stuck.
- Do not use this for "what percentage of users fail this" or "did time on task improve".
  Those are rate questions: route them to `quant-usability-metrics`, or `ab-test-design` if
  the comparison runs live.

## Gather first

1. The decision this informs and the flow under test.
2. What exists to test: clickable prototype, staging build, or production.
3. The segment, and whether they have used the product before.
4. Sessions available and the deadline.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Choose fidelity by what you are testing.** For labels, layout, or comprehension, low
   fidelity is fine and faster. For completing a flow, trusting an output, or recovering from
   an error, use something interactive with real-looking data. Never test generated output with
   placeholder text. The content is what is being judged. If the question is purely "where would
   you expect to find X", use `tree-test-design` instead.
2. **Run five to eight participants per distinct segment.** Five surfaces most severe problems
   in one segment, and covers no second segment. A new-user flow and a returning-user flow are
   two studies.
3. **Write three to five tasks, each a goal plus a trigger plus a constraint.** Give a reason
   to be there and a state of the world, then get out of the way. Take the feature under test by
   name from the product context, then write the task with that name absent.
   - Bad: any sentence that names the feature or the product's own metric and states the goal the
     way the product states it. That tests whether the participant can read the interface.
   - Good, in shape: "You need to <outcome in the user's own words> before <deadline>. You have
     <what they would realistically be holding>. Get it to where you would be willing to <the
     real-world commitment>." Fill the brackets from the situation described in the product
     context, never from the interface.
   Scrub every feature name, button label, and interface heading word. If the participant cannot
   restate the task in their own words, rewrite it.
4. **Order tasks first-run first, then by dependency, then rotate the rest.** Discovery and
   first-impression tasks must run before the participant has learned the interface. That
   learning cannot be undone, so put the discovery-sensitive task at position one and accept the
   order effect on the rest.
5. **Give the think-aloud instruction once, at the start, and demonstrate it.** "Say what you
   are looking at, what you expect to happen, and what you are looking for. If you go quiet I
   will ask what you are thinking, and that is not a hint that anything is wrong." Model it for
   ten seconds on an unrelated screen. After that, only ever prompt "what are you thinking?"
6. **Never explain the interface mid-task.** No nudge, no hint, no "it is up at the top". The
   stuck is the data. Say "what would you try next?" and let them try it. Unblock only after the
   task is marked failed, and only to reach the next one. Common mistake: rescuing at 30 seconds
   of silence and losing the finding worth the whole session.
7. **Record per task during the session, not after.** Completion: unassisted, assisted, or
   failed, against a cutoff set in advance. Assists: count and what each one was. Errors: wrong
   path, and whether self-corrected. Hesitations: timestamp plus what was on screen. Expectation
   breaks: they said what they expected and it did something else. Keep quotes separate from
   interpretation, per `interview-moderation`.
8. **Rate severity, not frequency, after each session.** Each problem blocks the goal, causes a
   wrong result, costs significant time, or is cosmetic. Report "four of six hit this" as a raw
   count, never a percentage. Percentages on n=6 invite the team to argue about the number
   instead of fixing the problem.

## Output

```
# <Flow name>: Usability Test Plan
Decision · flow under test · fidelity · segment · n per segment · dates

## Tasks
Task 1. <goal + trigger + constraint, read verbatim to participant>
  Success criteria: <observable end state>
  Cutoff: <time>   Watch for: <specific suspected failure>

## Session protocol
Warm-up · think-aloud instruction and demo · tasks in order · post-task question
("what would you have done next on your own?") · wrap-up

## Per-task record sheet
| Task | Completion | Assists | Errors | Hesitation points | Verbatims |

## Findings
Problem · severity · raw count (x of n) · evidence · suspected cause
```

## Quality bar

- No task contains a feature name, a button label, or a word from the interface.
- Every task states a trigger and an observable success criterion, and the
  discovery-sensitive task is first.
- Completion, assists, errors, and hesitation points each have a live recording slot.
- Findings use raw counts, and the plan says in one line that it cannot estimate rates.
- The sample is one segment per five to eight sessions, not two segments in one batch.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now, before a session is scheduled. It is a mandatory gate: no
  study is fielded without it.
- Invoke `sampling-plan` and `screener-builder` next unless the user redirects, then
  `participant-comms` for the invites.
- Invoke `interview-moderation` to turn the protocol into a moderator runbook, and
  `session-debrief` within an hour of each session.
- If the question is a rate or a benchmark rather than a list of problems, invoke
  `quant-usability-metrics` instead.
- Invoke `insight-to-spec` for each problem `so-what-checker` has cleared.
