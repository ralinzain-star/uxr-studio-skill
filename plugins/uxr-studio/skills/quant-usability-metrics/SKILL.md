---
name: quant-usability-metrics
description: >
  This skill should be used when the user asks "what percentage of users struggle with this",
  wants to "benchmark usability", "measure task success", or needs usability numbers with
  confidence intervals rather than a list of observed problems. Produces an unmoderated
  task-based study plan with success criteria, metrics, sample size, and a benchmark protocol.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# Quantitative Usability Metrics

A five-person usability test finds problems and cannot tell you how common they are. Those are
two different studies. When someone asks what percentage of users struggle, stop reaching for
another round of moderated sessions and run this instead: unmoderated, task-based, at a sample
that supports a rate.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- Someone needs a rate: how many users complete a task, how long it takes, how many fail.
- A known problem needs sizing before it competes for roadmap space.
- A redesign needs a before-and-after number, or a release-over-release benchmark.
- Do not use this when the goal is to discover what is wrong or why. Use `usability-test-plan`
  for a small-n diagnostic round, and run that first if the failure modes are still unknown.

## Gather first

1. The tasks, and what a stakeholder would accept as success for each, stated as an observable
   end state.
2. The population and how it is recruited, checked against the sampling trap named in the
   product context.
3. Whether this is a one-off read or the first point in a benchmark series.
4. Whether a prior small-n round already named the failure modes.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the success criterion before fielding, as an observable end state.** Build the
   contrast pair from one of the features named in the product context: the weak version is a
   mental state ("the user understands X"), the strong version is a specific screen reached or
   artifact produced inside the task window. Ambiguous criteria get graded after the fact, which
   is how a 45% success rate becomes a 70% one.
   **Hard stop: if the success criterion a stakeholder would accept for each task is not
   known, do not continue.** Ask for it with AskUserQuestion and wait. Never infer it, never
   write "TBD", and never proceed assuming it will be agreed once the data is in. A criterion
   settled after fielding is a criterion settled by the result. The only exception is an
   explicitly unattended run, in which case emit the verdict `Blocked: no agreed success
   criterion` and nothing else.
2. **Cap the study at 5 tasks**, each under 4 minutes, ordered from most to least important
   because fatigue is real and later tasks lose data. Tasks must be independent; if task 3
   depends on task 2 succeeding, the denominators break.
3. **Run it unmoderated.** A moderator changes success rates by prompting, and cannot be present
   for 100 sessions. Record screen and audio, but grade against the criterion, not against your
   impression of the session.
4. **Collect exactly these five metrics.** Task success as a binary against the pre-defined
   criterion. Time on task, computed on successful attempts only and reported as a median, since
   the distribution is right-skewed and the mean is meaningless. Error count against a
   pre-listed error taxonomy. The single ease question immediately after each task, 7-point,
   "Overall, how difficult or easy was this task?" A standardized post-test usability score at
   the end, the 10-item System Usability Scale, unchanged, so it stays comparable.
5. **Size the sample for the interval you need, not for a p-value.** For a success rate near 50%,
   the 95% interval half-width is roughly plus or minus 14 points at n=50, 10 points at n=100,
   7 points at n=200, and 5 points at n=385. Take n=100 as the floor for a headline rate, and
   n=100 per cell for any segment comparison. If the budget only supports 30, say plainly that
   the study cannot produce a rate and offer the diagnostic study instead.
6. **Set exclusion rules in advance**, the same way as any survey: completion under one third of
   median time, no task attempted, obviously automated behavior. Over-recruit 20% for attrition.
7. **Benchmark by freezing the instrument.** Same tasks, same wording, same success criteria,
   same recruitment source, same metrics, re-run each release or each quarter. Changing a task
   between waves resets the series to n=1. Keep a versioned protocol file and log every change
   with the date it took effect.
8. **Report each rate with its interval and against the prior wave**, and treat a wave-over-wave
   change smaller than the combined intervals as no movement, not as a trend.

## Output

- **What this study can and cannot answer** — rates yes, causes no.
- **Tasks** — number, participant-facing wording, success criterion, error taxonomy, time cap.
- **Sample** — n, segments and per-cell n, recruitment source, geography, exclusion rules.
- **Metrics table** — metric, definition, how computed, reporting form.
- **Results** — per task: success rate with interval, median time, error rate, ease score. Then
  the post-test score with its interval.
- **Benchmark row** — this wave against prior waves, with the movement verdict.
- **Problems observed** — brief, and explicitly flagged as diagnostic, not as sized findings.

## Quality bar

- Every success criterion is an observable end state written before fielding.
- n is at least 100 for any headline rate and any compared cell, or the study is redefined.
- Every rate carries a confidence interval; time on task is a median over successes only.
- Task wording avoids product vocabulary and passes the participant-facing language rules in
  the product context.
- The protocol is versioned so the next wave can be run identically.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now unless it has already cleared this study. It is a
  mandatory gate: nothing fields before the harm review.
- If the failure modes are not yet known, invoke `usability-test-plan` first; sizing an unnamed
  problem measures nothing.
- Invoke `sample-size-advisor` next unless the user redirects, then `sampling-plan` to confirm
  the recruitment frame.
- Invoke `so-what-checker` now on the sized results. It is a mandatory gate before any rate is
  shown; then `insight-writer` and `impact-tracker` so each wave stays retrievable.
