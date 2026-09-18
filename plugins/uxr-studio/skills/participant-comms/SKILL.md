---
name: participant-comms
description: >
  This skill should be used when the user asks to "write the invite", "draft a reminder",
  "we keep getting no-shows", "send a thank-you to participants", or needs the full set of
  messages around a scheduled research session. Produces a timed message sequence with
  ready-to-send copy for invite, confirmation, reminders, reschedule, no-show and thank-you.
metadata:
  version: "0.1.0"
  stage: "recruiting"
---

# Participant Comms

No-shows are a comms failure more often than a participant failure. The sequence and its timing
do more work than the wording, so build the schedule first and write the copy into it. Keep every
message short, put the time cost and the incentive in the first two lines, and never describe the
study in a way that tells the participant what you hope to hear.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A study is recruiting and you need the participant-facing messages before the first session.
- No-show rate is above 15% and the fix is being looked for in the wrong place.
- A session needs rescheduling or a participant did not turn up and you need the follow-up.
- Do not use this when the question is what the incentive should be or how consent is worded —
  use `incentives-and-consent`, then write those numbers into these messages.

## Gather first

1. Session length, format (moderated call, unmoderated, diary, in-context), and recording plan.
2. The incentive amount, form, and payment timing, from `incentives-and-consent`.
3. The sender identity and channel: research inbox, `~~scheduling tool` automation, `~~chat`.
4. Recruiting source, since panel participants and your own customers need different framing.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Build the sequence before the copy.** Seven touches, fixed timing:
   - **Invite** — on qualification, with the booking link. Expires in 72 hours, say so.
   - **Confirmation** — within 5 minutes of booking, automated, includes calendar attachment,
     join link, incentive, and the recording statement.
   - **Reminder 1** — 24 hours before. Includes a one-tap reschedule link.
   - **Reminder 2** — 1 hour before. Join link only, two sentences, no new information.
   - **Live nudge** — 4 minutes after start if the participant has not joined. Send on the
     channel they replied on fastest, not on email.
   - **No-show follow-up** — within 2 hours, warm, offers one rebook, no guilt.
   - **Thank-you and payment confirmation** — same day, with the payment reference.
2. **Treat the 1-hour reminder as the highest-leverage message.** Most no-shows are people who
   forgot, not people who declined. If you can only add one touch, add this one.
3. **Always offer a reschedule path in every pre-session message.** A participant with no easy
   exit becomes a no-show. A reschedule costs a slot; a no-show costs the slot and the incentive
   decision.
4. **Put time cost and incentive in the first two lines of the invite.** Length in minutes, the
   amount, and when it is paid. Everything else is below the fold.
5. **Describe the study at the level of the activity, not the hypothesis.** Say what the
   participant will do, not what you are trying to learn. Build the contrast pair from this
   study's own topic: the weak version names the research hypothesis, the strong version names
   only the activity and the period it covers. Naming the behavior under study, or using the
   product's own framing from the product context, primes the session before it starts.
6. **Disclose recording in the confirmation, not at the session.** One line: what is recorded,
   who sees it, and that they can stop at any time. Full wording comes from
   `incentives-and-consent`.
7. **Run every line against the language and tone rules in the product context.** They bar
   specific framings and specific outcome claims for this population. Apply them to subject
   lines and placeholders too, not just body copy.
8. **Cap each message.** Invite 120 words. Confirmation 100. Reminder 1 60. Reminder 2 30.
   No-show follow-up 70. Thank-you 60. If a message runs long, the study is under-scoped, not
   the message.
9. **Send from a named human at a research address**, not a no-reply. Reply rate on the reschedule
   path drops sharply from no-reply senders.
10. **Log every send and response in `~~project tracker`** so the no-show rate is measurable per
    source. Without that, you cannot tell a comms problem from a `~~recruiting panel` quality
    problem.

## Output

A single comms pack:

- **Sequence table** — message, trigger, timing offset, channel, word cap.
- **Copy blocks** — one per message, ready to paste, with `{{placeholders}}` for name, date,
  time, timezone, join link, reschedule link, amount, payment reference.
- **Reschedule and no-show branch** — what fires when, and the one-rebook rule.
- **Do-not-say list** — the specific phrases barred by the product context for this study.
- **Tracking fields** — what to log per participant in `~~project tracker`.

## Quality bar

- All seven touches are present with explicit timing offsets.
- Time cost and incentive appear in the first two lines of the invite and again in the
  confirmation.
- Every pre-session message contains a reschedule link.
- No message states the hypothesis, uses the product's own framing, or breaks any
  participant-facing language rule in the product context.
- Every message is at or under its word cap and free of placeholder text left unfilled.
- Recording, who sees it, and the right to stop appear in the confirmation.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If the incentive amount or the recording language is not final, invoke
  `incentives-and-consent` before anything sends.
- Invoke `research-ethics-review` now unless it has already cleared this study. It is a
  mandatory gate: no participant is contacted before the harm review.
- Invoke `recruiting-quality-check` next unless the user redirects, using the scheduling and
  reply signals this sequence produces.
- If the sessions are moderated, invoke `interview-moderation`; if they are task-based, invoke
  `usability-test-plan` instead.
