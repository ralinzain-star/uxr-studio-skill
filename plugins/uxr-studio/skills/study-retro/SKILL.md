---
name: study-retro
description: >
  This skill should be used when the user asks to "run a retro on this study", "what did we
  learn from how we ran that research", "was that study worth it", or needs to close out a
  finished study. Produces a two-part retrospective that scores method execution and decision
  impact separately, with a small number of committed changes.
metadata:
  version: "0.1.0"
  stage: "ops"
---

# Study Retro

Retro the method and the decision impact separately. A study can be beautifully executed and
change nothing, and a scrappy one can move a roadmap. Grading them together lets good craft
disguise irrelevance, which is the most common way a research team gets quietly defunded.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study has shipped its report and the decision window has passed or is closing.
- A study went badly and the team wants to know what to change.
- A study went well and you want to know which part actually caused that.
- Do not use this when the subject is the team's whole practice rather than one study — use
  `research-process-audit`.

## Gather first

1. The original research question and the decision it was meant to inform.
2. What was actually decided, by whom, when, and whether the study was cited.
3. Cost: researcher days, incentive spend, participant count, calendar time from brief to report.
4. What changed in the product or the metrics since, from `~~product analytics` or
   `~~data warehouse`.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

Run the two tracks in order, method first, and never let a strong result in one excuse the other.

**Track 1: method execution.** Score each 1 to 5 with one sentence of evidence.

1. **Question quality** — was the question answerable and tied to a decision, or a topic?
2. **Sample frame** — did it match the population the decision applies to? Name the structural
   exclusions that turned out to matter, then check the frame against the sampling trap named in
   the product context. A study that walked into that trap scores low here regardless of how
   clean the moderation was, because the sample described a population the decision was never
   about.
3. **Method fit** — did the method produce the kind of evidence the claim needed?
4. **Execution** — recruiting quality, no-show rate, exclusions, moderation discipline.
5. **Synthesis discipline** — were the findings traceable to data, and were disconfirming cases
   handled or quietly dropped?
6. **Speed** — calendar time from brief to report, against the decision's deadline.

**Track 2: decision impact.** Answer all four, in writing, in plain language.

7. **What did the decision-maker actually do?** Name the decision, the date, and the artifact:
   a ticket in `~~project tracker`, a spec, a roadmap change, a kill. "The team found it
   interesting" is a no.
   **Hard stop: if what was decided, by whom, and on what date is not known, do not continue the
   impact track.** Ask for it with AskUserQuestion and wait. Never infer a decision from a
   reaction in `~~chat`, never write "TBD", and never proceed assuming the artifact will be
   located later. Track 2 scored on a remembered sentiment rather than a dated artifact is the
   retro grading its own homework. The only exception is an explicitly unattended run, in which
   case emit the verdict `Blocked: no dated decision artifact` and nothing else.
8. **What would we have learned if we had done nothing?** Write the counterfactual honestly.
   If the team would have reached the same conclusion from `~~product analytics`, from
   `~~support desk` tickets, or from three conversations, the study's marginal value was the
   confidence, not the finding, and it should have been scoped smaller.
9. **What was the cheapest version that would have supported the same decision?** Name it
   concretely: five interviews instead of twelve, an evidence sweep via `prior-evidence-check`,
   one question on an existing `~~survey tool` send. This is the most useful question in the
   retro and the one teams skip.
10. **Did the decision move, and in which direction?** Three outcomes: the decision changed
    because of this study; the decision was confirmed and shipped faster; the decision was
    unaffected. Only the first two count as impact. Record which, and what the evidence is.
11. **Check whether the decision is even observable yet.** Some loops are longer than the retro.
    Check the feedback-loop length in the funnel section of the product context against the time
    elapsed since the study. Where the loop is longer, do not score the outcome. Score Track 2 on
    the decision made, schedule the outcome check for the end of that loop, and hand the metric
    to `impact-tracker`.
12. **Commit to at most three changes.** One per track plus one free. Each written as a change to
    a default, with an owner and the next study it applies to. A retro that produces a list of
    ten observations produces zero changes.

## Output

- **Study line** — question, method, n, cost in researcher days and incentive spend, dates.
- **Track 1 scorecard** — six rows, score plus one evidence sentence each.
- **Track 2 answers** — the four questions answered in full sentences, plus the observability
  note if the loop is longer than the retro.
- **Impact verdict** — changed / confirmed-faster / unaffected, one line of evidence.
- **Cheapest viable version** — the concrete alternative design, in three lines.
- **Committed changes** — at most three, each with owner, the default it changes, and the next
  study it applies to.
- **Scheduled outcome check** — date and metric, when the loop is longer than the retro.

## Quality bar

- Method and impact are scored separately and neither score is used to explain the other.
- The counterfactual and the cheapest-version answers are both written, specifically, not skipped.
- The impact verdict cites a dated artifact, not a sentiment.
- Committed changes number three or fewer and each names an owner and a next study.
- Cost is stated in researcher days and spend, so the value question is answerable.
- Where the decision loop is longer than the retro, an outcome check is scheduled with a date.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `impact-tracker` now with any pending outcome metric and its scheduled read date. It is
  a mandatory gate at study close: an unlogged outcome is an impact claim nobody can audit.
- Invoke `research-repository-hygiene` to file the study record, its exclusions, and the
  committed changes as defaults.
- If the same failure appears in three consecutive retros, invoke `research-process-audit`
  instead of filing a fourth committed change.
- If a committed change alters how studies get scoped, invoke `research-roadmap` to apply it.
- Stop here once the changes are filed and the outcome check carries a date.
