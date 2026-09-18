---
name: research-ethics-review
description: >
  This skill should be used when the user asks to "review this study for ethics", "is this
  research design harmful", "check our consent and data handling", "do we need an ethics review",
  or is finalizing any study design before fielding. Produces a seven-dimension harm review with
  required changes and a verdict of proceed, proceed with changes, or redesign.
metadata:
  version: "0.1.0"
  stage: "ops"
---

# Research Ethics Review

Run this on every study, not only the ones that look sensitive. The harms that occur are
incidental rather than designed: a screener collecting more than it needs, a recording that
identifies someone to their employer, an incentive a broke participant cannot refuse.
Risky-looking studies already get scrutiny. Routine ones are where damage happens.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study design is complete and not yet fielded, including short or self-serve ones.
- A screener, survey, or guide collects anything beyond the minimum.
- A finding is about to be published with quotes, clips, or recordings.
- Do not use this when writing the consent and incentive language itself. Use
  `incentives-and-consent`. This skill reviews what that produced.

## Gather first

1. The instrument set: screener, guide or questionnaire, consent text, incentive terms.
2. Who is recruited, from where, and how that population maps to the product context segments.
3. What is recorded, where it is stored, who can reach it, for how long.
4. How the output is published: internal only, quotes, clips, external use.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

Review seven dimensions in order. Write a finding for each, plus "no change" or a specific
required edit. A dimension marked fine without evidence has not been reviewed.

1. **Consent quality.** Consent must be informed, specific, and revocable. Check the participant
   is told who runs the study, what is recorded, who sees it, how long it is kept, that they may
   skip questions, and that they may withdraw later and have their data deleted. Check the
   reading level. Consent buried in a scheduling confirmation, or only spoken at session start,
   does not count.
2. **Data minimization.** Go field by field through the screener and instrument and name the
   analysis requiring each. Anything without one is cut. Most real harm starts here: collected
   data gets stored, joined, and retained long past the study. Watch employer, salary,
   immigration status, health, and anything the product context's rules say must not be
   required.
3. **Retention and deletion.** Require a deletion date set at fielding, an owner, and a named
   location for recordings, transcripts, and screener responses. Raw data left in
   `~~transcription tool`, `~~survey tool`, and `~~scheduling tool` is what gets forgotten.
   "Until no longer useful" is not a retention policy.
4. **Re-identification risk in quotes and recordings.** Assume any quote reaches the
   participant's employer. Check each quote and clip for the combination of role, company size,
   location, and tenure that identifies someone without a name. Require generalized detail,
   redacted names, and a separate opt-in for any clip shown outside the team.
5. **Incentive coercion.** The incentive must respect the participant's time without overriding
   their judgment about what to share. Check that it is unconditional, that partial
   participation is paid, and that the amount is not distorting for this population. Payment
   contingent on finishing the session is coercive.
6. **Burden on a vulnerable participant.** Check session length, scheduling demands, technology
   requirements, and the emotional weight of the topic. Where the population may be under
   financial or personal stress as described in the product context, require shorter sessions,
   immediate unconditional payment, explicit permission to stop, and no task that makes someone
   relive a failure to earn payment.
7. **Harmful disclosure.** Ask whether any question invites a participant to reveal something
   that could hurt them: an employer learning they are looking, an admission about performance,
   a financial or legal situation. Rewrite so disclosure is volunteered, never requested, and
   never a condition of qualifying in the screener.

Then apply the population-specific rules. Pull the participant-facing language and tone rules
from the product context in full and check the instruments line by line against every one,
including fault-attribution, outcome-promise and required-disclosure rules. A violation is a
required change, not a note.

## Output

- **Verdict** — proceed, proceed with changes, or redesign, with one sentence of reasoning.
- **Dimensions** — seven rows: dimension, finding, evidence, required change.
- **Required changes** — numbered, with replacement wording where relevant, and an owner.
- **Population rules check** — each product context rule, pass or fail, with the failing line.
- **Publication conditions** — what may be quoted, what needs opt-in, what never leaves.

Verdict rules. **Proceed**: all seven dimensions pass. **Proceed with changes**: fixes are
wording, field removal, or handling, and land before fielding. **Redesign**: the harm is
structural, meaning wrong population, a coercive incentive that cannot be fixed, or a question
unanswerable without harmful disclosure.

## Quality bar

- All seven dimensions have a written finding, including those that pass.
- Every screener and instrument field is justified by a named analysis or cut.
- A deletion date, storage location, and owner are named for each raw artifact.
- Every required change carries replacement wording or an action, and an owner.
- Every participant-facing rule from the product context is checked individually.
- The verdict follows the stated rules, not overall impression.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If the verdict is redesign, stop here. Send the study back to `research-brief-builder`, and to
  `sampling-plan` when the population is the problem. Nothing is fielded until it returns.
- If the verdict is proceed with changes, invoke `incentives-and-consent` now for the consent and
  incentive wording and `participant-comms` for participant-facing copy. Every required change is
  applied before fielding, never after.
- If the verdict is proceed, invoke `recruiting-quality-check` next and field.
- If the population or the instrument changes later, invoke `research-ethics-review` again.
