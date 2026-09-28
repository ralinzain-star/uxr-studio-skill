---
name: incentives-and-consent
description: >
  This skill should be used when the user asks "how much should we pay participants",
  "what should the consent form say", "can we record this", or needs to set an incentive rate
  and obtain informed consent before fielding a study. Produces a rate recommendation with
  rationale, a payment policy, and a consent script and form ready to send.
metadata:
  version: "0.1.0"
  stage: "recruiting"
---

# Incentives And Consent

Incentives must be unconditional and paid promptly, including to people who quit halfway or turn
out not to qualify once the session starts. A conditional incentive buys compliance, and
compliance is the opposite of what a study needs. Consent is a plain-language statement of what
is recorded, who sees it, and how to stop, not a legal shield.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A study is about to field and the incentive has not been set or justified.
- Sessions will be recorded, transcribed, or shared beyond the research team.
- A participant wants their data removed, or asks what happens to the recording.
- Do not use this when the question is whether the study design itself is ethically sound
  (deception, risk, sensitive topics) — use `research-ethics-review` first.

## Gather first

1. Session length, format, and whether any pre-work or follow-up is required.
2. Audience seniority and how hard they are to reach.
3. Recording plan: audio, video, screen, transcript, and where each is stored.
4. Who will see the raw material beyond the researcher, and retention period.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Set the rate from time, not from generosity.** Start from a base and adjust:
   - Base, consumer audience: 25 USD at 15 min, 50 at 30, 75 at 45, 100 at 60.
   - Professional or specialist: 1.5x base. Senior decision-maker or buyer: 2.5x to 3x.
   - Add 30% for pre-work, homework, or an install step.
   - Diary and longitudinal: pay per entry as entries land, plus a completion bonus that is a
     top-up and never a gate. Example: 20 per daily entry, 50 at the end.
   - Unmoderated tasks: 1 USD per expected minute, minimum 15.
2. **Pay within 48 hours, and pay the full amount to anyone who starts.** If the session ends at
   minute four, they are still paid in full. Announce this in the invite: it improves honesty
   because there is nothing left to earn by performing.
3. **Never make payment contingent** on completing tasks, answering all questions, staying the
   full time, giving a positive rating, or qualifying on arrival. If a participant is
   disqualified in the first minutes, pay them and end warmly.
4. **Choose a form that reaches the person, not the one that is easy to expense.** Prefer direct
   digital payment or a widely redeemable gift card. Never make product credit the only option:
   it pays people in the thing you are studying and biases every answer about its value.
5. **Apply the vulnerable-population rules from the product context.** Participants may be
   unemployed, financially stressed, or recently laid off. So: pay immediately and
   unconditionally, never in a monthly batch. Never require anyone to name an employer, disclose
   a salary, or recount a rejection they did not raise themselves. Never imply fault for their
   outcomes or promise a job, interview, or hiring result in any of this language.
6. **Write consent to cover six things, in plain sentences:** what is recorded (audio, video,
   screen); who sees the raw recording versus only quotes and clips; whether the record is
   pseudonymized; how long it is kept and where; the right to skip any question or stop at any
   moment with no effect on payment; and the right to withdraw their data afterward, with a
   deadline and an address to use.
7. **Set a concrete retention period**, as a date rule and not "as long as needed." Default:
   raw recordings deleted at 12 months, transcripts pseudonymized in `~~research repository`.
8. **Get consent twice.** Written at booking, verbal and recorded in the first minute. The
   verbal one is the one that holds up, because it proves they heard it.
9. **Honor withdrawal fully** within a stated 30-day window: delete the recording, remove the
   transcript from `~~research repository`, strike verbatims from unpublished output. Say in the
   form that already-published aggregate findings cannot be unpicked.
10. **Never trade a bigger incentive for a weaker consent.** If a participant declines recording,
    run with notes only and pay the same.

## Output

- **Rate recommendation** — amount and the formula line that produced it.
- **Payment policy** — form, timing, partial-session rule, disqualified-on-arrival rule.
- **Consent form** — plain language, under 400 words, covering the six items above.
- **Verbal consent script** — 4 to 6 sentences, read at the top, ending in an explicit spoken yes.
- **Withdrawal procedure** — who to contact, the 30-day window, what gets deleted.
- **Vulnerable-population notes** — the specific do-nots applied to this study.

## Quality bar

- The rate is derived from a stated formula, not asserted.
- Payment is unconditional and within 48 hours, and the invite copy says so.
- The consent form names what is recorded, who sees it, retention, and both rights in plain
  words a non-specialist reads once.
- A verbal consent script exists and asks for an explicit spoken yes.
- Nothing in any participant-facing text implies fault or a job outcome, or requires disclosure
  of employer, salary, or an unvolunteered rejection.
- Retention is a specific period, not a vague phrase.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now unless it has already cleared this design. Mandatory
  gate: nothing is fielded on an incentive and consent pack alone.
- Invoke `participant-comms` next unless the user redirects, so the amount, the timing, and the
  recording sentence appear in the invite and confirmation.
- Invoke `recruiting-quality-check` next unless the user redirects, since an unconditional
  incentive attracts fraudulent participants.
- If a participant withdraws, invoke `research-repository-hygiene` to strike the transcript
  within the 30-day window.
- Stop here once the invite carries the rate and the verbal script is with the moderator.
