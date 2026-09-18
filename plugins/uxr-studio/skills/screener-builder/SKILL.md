---
name: screener-builder
description: >
  This skill should be used when the user asks to "write a screener", "build a screening
  survey", "who should we recruit for this", or needs to filter a recruiting pool down to
  qualified participants without telling them what qualifies. Produces a numbered screener
  with disguised criteria, scored answer options, and an explicit qualify/disqualify key.
metadata:
  version: "0.1.0"
  stage: "recruiting"
---

# Screener Builder

A screener's job is to exclude the wrong people, not to describe the right ones. Every question
must be behavioral and disguised, because the moment a respondent can infer the target answer,
the incentive teaches it to them. Never ask "are you a frequent user of X" — a paid respondent
will say yes, and you will have bought a confident liar.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study needs participants and you have criteria in someone's head but not on paper.
- A previous round produced participants who did not match the brief and you are rewriting.
- A panel vendor or `~~recruiting panel` has asked for screening questions in their format.
- Do not use this when the question is *where* to source people from or how many to take from
  each pool — use `sampling-plan` instead, then come back here to write the questions.

## Gather first

1. The research question and the decision it feeds.
2. The target segment, in behavior terms, not identity terms.
3. Who must be excluded and why (competitors, employees, prior participants, wrong geography).
4. Sample size and quota cells, if `sampling-plan` has already set them.
5. Where this will be fielded: `~~recruiting panel`, in-product intercept, customer list.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the qualify key first, before any questions.** One line per criterion: the exact
   behavior, the threshold, and the recency window, in the shape "<did concrete countable thing>
   <N or more times> in the last <window>". Build the criteria from the product context: one on
   the frequency of the core action, one on recent use of the feature or funnel stage the study
   is about, drawn from the features and funnel sections by name. If a criterion cannot be
   written as an observable count in a window, it is an identity claim and does not belong in a
   screener.
2. **Convert each criterion to a frequency question with a neutral scale.** Symmetric, evenly
   spaced, no obvious right end. Five to seven options, both tails plausible: "None / 1–2 / 3–5 /
   6–10 / 11–20 / More than 20." Never yes/no for a behavior that has a rate.
3. **Hide the target criterion among plausible distractors.** For any question naming an
   activity, tool, or feature, list four to six siblings a real person would also do and score
   only the one you care about. Build that list from the product context: the other features
   named there, plus adjacent activities implied by its description of who the users are and
   what they are trying to get done. Every option must be plausible for the target population,
   and the scored one must not be the only one that fits. Never let a single item stand alone.
4. **Anchor every behavior to a recency window.** Default to "in the last 30 days" for active
   behavior and "in the last 90 days" for purchase or cancellation behavior. Lifetime framings
   ("have you ever") qualify everyone and mean nothing.
5. **Plant one professional-respondent trap.** Include a fictional item in one multi-select
   list. Anyone who selects it is out, no appeal. Also disqualify on straight-lining an entire
   grid and on completing the screener faster than half the median time.
6. **Screen on behavior over self-described identity.** Never read a segment label from the
   product context back to a respondent and ask whether it describes them. Ask the two or three
   factual questions whose answers place them in a cell, and derive the segment yourself.
7. **Order the questions so the cheap kills come first.** Geography, then exclusions, then
   behavior frequency, then segment derivation, then the open-ended.
8. **End with one open-ended question**, two sentences minimum: "In your own words, what are
   you trying to accomplish right now?" It is the fraud check and the best single predictor of a
   useful session. Score it, do not just collect it.
9. **Keep it under 10 questions and 3 minutes.** Longer screeners raise cost and attract
   exactly the professional respondents you are trying to exclude.
10. **Check it against the participant-facing language rules** in the product context. A screener
    is participant-facing. Do not use the product's own framing in a question.

## Output

Produce a single document:

- **Study and target** — one line each.
- **Qualify key** — the criteria table: criterion, question number, qualifying answers.
- **Screener questions** — numbered, with every answer option written out and marked
  `[QUALIFY]`, `[DISQUALIFY]`, or `[QUOTA: cell]`.
- **Termination logic** — which question numbers terminate and the exact termination message.
- **Quota table** — cell, target n, which question assigns it.
- **Open-ended scoring rubric** — three lines: what a pass, a borderline, and a fail look like.
- **Assumptions** — anything inferred from the product context rather than supplied.

## Quality bar

- Every criterion in the qualify key maps to at least one numbered question.
- No question reveals the target answer, and no answer option is the only plausible one.
- Every behavioral question carries a recency window and a symmetric scale.
- Exactly one fictional-item trap exists, and the terminate logic for it is written.
- The screener runs in 3 minutes or less and contains no product framing or outcome promise.
- Geography is asked explicitly and the sample's geography is stated in the quota table.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. It is a mandatory gate: no screener reaches a participant
  before consent, data minimization and incentive coercion have been checked.
- Invoke `recruiting-quality-check` next unless the user redirects, to build the live-session
  detection list from this qualify key and trap item.
- Invoke `incentives-and-consent` to set the rate and the consent script before fielding.
- If respondents are scheduling, invoke `participant-comms` for the invite and reminder sequence.
- If the qualify key has no frame behind it, invoke `sampling-plan` instead.
