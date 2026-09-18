---
name: impact-tracker
description: >
  This skill should be used when the user asks to "track research impact", "log what happened
  after that study", "did anyone act on this research", "build an impact log", or needs a
  quarterly rollup of what research changed. Produces dated impact entries with decision states,
  scheduled follow-up reads, and a rollup that feeds funding and audit work.
metadata:
  version: "0.1.0"
  stage: "impact"
---

# Impact Tracker

Log the decision at the moment it is made. Impact reconstructed at review season is
unconvincing and mostly wrong, because the people who remember are the ones who liked the study.
A study whose decision was never recorded counts as zero impact, not as a quiet success. The
zeros are what make the rest of the log credible when someone audits it.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study is closing and its entry must be opened before anyone forgets the intended path.
- A decision has just been made in a review, a planning session, or `~~chat`.
- A quarter is ending and the rollup is due.
- Do not use this when the task is valuing the logged decisions in money. Use `research-roi`,
  which reads from this log.

## Gather first

1. The study: title, dates, question, method, cost, and the decision it was meant to inform.
2. The intended path before the research, captured before the readout if at all possible.
3. The named decider, not the team. A team cannot be asked what it decided.
4. Where the decision artifact lives: `~~project tracker` ticket, spec, or doc link.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Open the entry when the study closes, not when the decision lands.** Record the question,
   the recommendation, the named decider, the expected decision date, and the intended path
   before the research. That pre-registered path is the only thing that makes a counterfactual
   defensible later, and it cannot be written honestly after the fact.
   **Hard stop: if the decision this study informs or the individual who owns it is not known,
   do not continue the entry.** Ask for them with AskUserQuestion and wait. Never infer a
   decider, never name a team, never write "TBD", and never proceed assuming the owner will
   turn up later. An entry with no named decider cannot be audited and counts as zero impact,
   which is the whole point of the log. The only exception is an explicitly unattended run, in
   which case emit the verdict `Blocked: no named decider` and nothing else.
2. **Log the decision within 48 hours of the meeting where it was made.** Quote the decider's
   own words. An entry written a week later has already turned into a summary.
3. **Assign one of six decision states. No free text in this field.**
   - **Adopted** — shipped substantially as recommended.
   - **Adopted with changes** — shipped in modified form. Record what was dropped and why.
   - **Stopped** — the research ended a planned build or experiment. Record the scope stopped.
   - **Deferred** — decision postponed with a named date. Deferred with no date is No decision.
   - **Superseded** — later evidence replaced the finding. Link the study that replaced it.
   - **No decision** — nothing recorded within one week of the expected date. It is the default
     state and does not convert to a win later without a dated artifact.
4. **Schedule the follow-up read at study close, not after ship.** Set the read date as ship
   date plus the feedback-loop length from the product context for the relevant funnel stage.
   Name the metric from `decision-metrics-mapper` and the owner of the read. A follow-up nobody
   has scheduled is a follow-up that does not happen.
5. **Record the counterfactual evidence at decision time.** Which of the three tests in
   `research-roi` passed: intended path documented beforehand, decider states they would have
   gone otherwise, alternative path was resourced. Recording it later is guessing.
6. **Do not edit history.** When the state changes, append a dated line. An entry that shows
   Deferred, then Adopted with changes, then a read result is more persuasive than one that only
   ever showed success.
7. **Run the quarterly rollup from the states, not from narrative.** Count studies by state,
   compute the share with a dated decision artifact, name every No decision entry, and report
   which due reads were taken. Lead with the No decision count.
8. **Store entries in `~~research repository` beside the study, linked to the matching
   `~~project tracker` item.** A log in a private spreadsheet loses its authority the moment its
   owner changes roles.

## Output

Entry, one per study:

- **Study** — title, dates, question, method, cost, link.
- **Recommendation** — one sentence.
- **Intended path before research** — recorded pre-readout, with the date it was recorded.
- **Decider** — named individual.
- **State** — one of the six, with the date and the decision artifact link.
- **What changed** — in the decider's words.
- **Counterfactual tests passed** — which of the three, with evidence.
- **Follow-up read** — metric, baseline, read date, owner.
- **History** — dated append-only lines.

Quarterly rollup:

- Counts by state, No decision first and named.
- Share of studies with a dated decision artifact, and the trend against last quarter.
- Reads due this quarter, taken versus missed, and what they showed.
- Studies whose findings were superseded or contradicted.

## Quality bar

- Every entry names an individual decider, never a team.
- The intended path carries the date it was recorded, and that date precedes the readout.
- Every state is one of the six, with a linked artifact for anything other than No decision.
- Every Adopted or Adopted with changes entry has a scheduled read with an owner and a date.
- The rollup leads with the No decision count and names those studies.
- No entry has been rewritten. State changes appear as appended dated lines.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-roi` next unless the user redirects, on the quarterly rollup, to value the
  logged decisions.
- Invoke `business-case-builder` next unless the user redirects, if funding or headcount is
  being argued this cycle.
- If the rollup shows contradicted or superseded findings, invoke `research-repository-hygiene`
  to retire them.
- If No decision is the largest state, invoke `research-process-audit` instead of reporting the
  rollup as impact.
- Stop here after logging a single entry; only the rollup feeds anything downstream.
