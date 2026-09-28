---
name: research-shareout
description: >
  This skill should be used when the user asks to "present the findings", "run the readout",
  "prep for the shareout", or needs to turn a finished report into a meeting that produces
  decisions. Produces a run of show, handling scripts for the predictable derailments, and a
  recorded decision log.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# Research Shareout

The shareout is a decision meeting, not a presentation. Budget more than half the clock for
discussion and open by naming the decision on the table. If no decision is on the table, cancel
the meeting and send the report. A room assembled to hear findings read aloud is an expensive way
to distribute a document that everyone could have read faster alone.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A report is finished and a decision needs to be made by a group.
- Findings contradict a plan that is already resourced and the disagreement has to happen live.
- Several teams must align on which opportunity gets worked next.
- Do not use this when the aim is awareness rather than a decision. Use `research-newsletter`
  or just send the `pyramid-report`.

## Gather first

1. The decision on the table, phrased as a choice between named options.
2. Who in the room can make that decision, and who can block it.
3. The finished report and the findings that cut against any attendee's current plan.
4. The meeting length and whether pre-read is realistic.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the decision sentence and put it in the invite.** Shape: "By the end of this hour we
   decide whether to X or Y, and who owns it." If you cannot write that sentence, cancel.
2. **Send the report at least 24 hours ahead and say it will not be presented.** Then open with a
   five-minute recap anyway, because roughly half the room will not have read it. Recap the answer
   and the recommendation only. Never walk the deck.
3. **Budget the clock explicitly.** For a 60-minute slot: 5 recap, 10 evidence for the two or
   three arguments most likely to be contested, 30 discussion and decision, 10 recording owners
   and next steps, 5 slack. If discussion falls under half, the agenda is a presentation.
4. **Pre-empt the contested arguments.** Identify in advance the two or three findings that
   threaten someone's plan and prepare the evidence for those, not for the uncontroversial ones.
5. **Handle the stakeholder who relitigates the method.** Distinguish two cases out loud. If the
   challenge is real and would change the conclusion, say so, state what the study can and cannot
   support, and downgrade the claim in the room. If the challenge is a proxy for disliking the
   result, name the standard: "What sample would you find convincing, and would you commit to the
   decision if we got it?" Then either commit to running it or move on. Never argue sample size
   line by line. Take method debates offline once the answer to that question is recorded.
6. **Handle a finding that contradicts a sponsor's plan.** Do not soften the finding and do not
   stage an ambush. Tell the sponsor before the meeting, in private, with the evidence. In the
   room, state the finding flatly, then immediately name what is still true about their plan and
   what the smallest change would be. Give them a path that is not a reversal: a scoped test, a
   staged rollout, a changed success metric. Attack the plan's assumption, never the sponsor.
7. **Force the decision or record the block.** If the room will not decide, do not let it dissolve
   into "let us take this away". Record which of these is true: decided, deferred to a named
   person by a named date, or blocked pending a named piece of evidence.
8. **Close by reading the decisions and owners back aloud, in the room, before anyone leaves.**
   A decision recorded after the fact gets edited by whoever remembers it best. Post the log in
   `~~chat` within the hour and file it in `~~research repository` beside the report.

## Output

```
Invite            decision sentence, pre-read link, stated no-walkthrough
Run of show       minute-by-minute blocks with owners
Recap script      the answer and the recommendation, 5 minutes
Contested set     2-3 findings + the evidence slide each needs
Derailment prep   method challenge script, sponsor-conflict script, per person
Decision log      decision | option chosen | owner (role + name) | date | evidence that would reverse it
Parked            method questions and out-of-scope items, with owners
```

## Quality bar

- The decision sentence exists and names options, not topics.
- Discussion time exceeds presentation time in the run of show.
- Every attendee who can block the decision is identified and pre-briefed if contradicted.
- Scripts exist for the method challenge and the sponsor conflict, written as words to say.
- The decision log has a named owner and a date for every line, including deferrals.
- The log was read aloud in the room and posted the same day.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `impact-tracker` now with the decision log. It is a mandatory gate: the study closes
  here, and an unlogged decision cannot be measured later.
- Invoke `insight-to-spec` next unless the user redirects, once per accepted recommendation.
- If a recommendation was accepted but not funded, invoke `opportunity-backlog` instead.
- If the room blocked or deferred, invoke `decision-metrics-mapper` to name the number that would
  unblock it and when it can be read.
- Invoke `study-retro` to close the study out.
- Stop here once every line of the log has an owner and a date.
