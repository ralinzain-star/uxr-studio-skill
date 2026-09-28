---
name: diary-study-design
description: >
  This skill should be used when the user asks to "run a diary study", "design an experience
  sampling study", "track behavior over weeks", or needs to observe a behavior that unfolds
  over days rather than inside one session. Produces a diary protocol: cadence, prompt wording,
  entry format, compliance plan, and the mandatory exit interview guide.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Diary Study Design

Diary studies fail on compliance, not on design. Build the prompt schedule around the rhythm the
participant already has, keep every entry under two minutes, and treat entries as evidence that
something happened rather than as an explanation of why. The explanation comes from the exit
interview, which is not optional.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- The behavior has a long feedback loop. Products with slow loops are exactly where a diary
  study repays its cost. Check the feedback-loop length in the funnel section of the product
  context: when it runs longer than a session, a one-hour interview cannot see the loop at all.
- You need frequency and sequence, not recall, or the emotional arc decays fast. Anchor this in
  a moment from the standing problem areas in the product context whose feeling fades in days.
- Do not use this when you need to watch someone's environment and workarounds in situ. Use
  `contextual-inquiry`. Do not use it to test a specific design. Use `usability-test-plan`.

## Gather first

1. The behavior being tracked, stated as an observable event with a trigger.
2. The study window and why that length, tied to one full cycle of the behavior.
3. The decision this feeds and the date that decision is made.
4. Recruiting source and incentive budget, since diary incentives are paid in stages.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Choose cadence from the shape of the behavior.** Episodic, bursty behavior takes
   event-triggered entries: the participant logs when the thing happens and you nudge at most
   once a day. Continuous behavior takes time-triggered entries on a fixed schedule. Episodic
   behavior on a time schedule produces pages of "nothing happened today," the most common way
   these studies die. Classify the behavior named in the product context as episodic or
   continuous before choosing. A behavior arriving in evening bursts wants event triggers, not
   a daily 9am ping.
2. **Anchor the prompt to an existing rhythm.** Ask in the screener when the behavior usually
   happens and schedule per participant, not on one global clock. A prompt inside a routine gets
   answered. One that interrupts a routine gets dismissed.
3. **Cap the entry at two minutes and enforce it structurally.** Three to five fields: one
   closed field for what happened, one short scale, one free-text of a sentence or two, one
   optional artifact upload. Never ask "why" in a diary entry. Participants write a tidy
   rationalization and you will believe it.
4. **Prompt for a moment, not a summary.** "What was the last thing you did on this, and what
   happened right after?" beats "How did this week go?" Summary prompts get summary answers.
5. **Set the window to one full cycle plus a week.** Two weeks is the floor for episodic
   behavior. Under one week this is experience sampling, so call it that and shorten the exit.
6. **Plan for drop-off before it happens.** Over-recruit 30 to 50 percent and expect entry
   quality to decay from about day four. Stage the incentive across day one, the check-in, and
   the exit. Never pay it all at the end, and never condition payment on a minimum entry count
   for a population under financial stress.
7. **Run a kickoff with one practice entry, watched live.** Most compliance failures are tooling
   failures discovered on day three.
8. **Run a 10-minute mid-study check-in with every participant.** Highest-leverage move in the
   method: it rescues the ones who have gone quiet, catches misreadings of the prompt while
   there is study left, and proves a human is reading. Schedule it at the midpoint when you
   book the kickoff, not when you notice silence.
9. **Run an exit interview against the participant's own entries.** Read their log first, pick
   four to six entries, and walk them back through each: what was happening, what you did next,
   what you expected. Pattern questions come last. Skip this and you own a timestamped event
   list with no causation.
10. **Code entries daily, 15 minutes.** It keeps exit interviews sharp and surfaces the probe
    worth adding in week two.

## Output

- **Study question and decision** — one line each, with the decision date.
- **Design summary** — cadence and why, window length, target n, over-recruit n.
- **Participant schedule** — kickoff, entry period, check-in day, exit interview.
- **Entry form** — every field verbatim, with type, guidance, and estimated completion time.
- **Prompt and nudge copy** — trigger, daily nudge, quiet-participant nudge, all checked against
  the participant-facing language rules.
- **Incentive schedule** — amount and condition at each of the three stages.
- **Check-in script** — five questions, 10 minutes.
- **Exit interview guide** — entry walkback first, pattern questions second.
- **Analysis plan** — the coding frame and how entries and exit transcripts combine.
- **Assumptions** — anything inferred from the product context.

## Quality bar

- Cadence matches behavior shape, and the document states which shape it assumed and why.
- One entry completes in two minutes, demonstrated by field count and field types.
- No diary prompt asks "why".
- The mid-study check-in and the exit interview are scheduled, not merely recommended.
- The incentive is staged, immediate at each stage, and not conditional on entry volume.
- The window covers at least one full cycle of the behavior.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: a multi-week study on a financially
  stressed population is not fielded until it clears.
- Invoke `incentives-and-consent` next unless the user redirects, to stage the payment, then
  `participant-comms` for the nudge copy.
- Invoke `interview-guide-builder` next unless the user redirects, on the exit guide, then
  `interview-moderation` before the first exit session.
- Invoke `session-debrief` after each exit interview, then `thematic-coding` and
  `affinity-synthesis` on the combined corpus.
- Stop here if one session would see the whole loop; use `contextual-inquiry` instead.
