---
name: recruiting-quality-check
description: >
  This skill should be used when the user asks "is this participant real", "this person seems
  fake", "should I keep this session", or needs to detect fraudulent, coached, or mismatched
  participants before and during a session. Produces a detection checklist, a scripted graceful
  exit, and a keep-or-discard ruling for the data.
metadata:
  version: "0.1.0"
  stage: "recruiting"
---

# Recruiting Quality Check

One fraudulent participant does more damage than three missing ones. A fake is confident, fluent,
and agreeable, so their quotes are the easiest to write down and they will dominate synthesis
while the honest, halting participant gets one line. Detect before the session where you can,
inside three minutes where you cannot, and discard aggressively.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Screener responses are in and need review before anyone is invited.
- A session is starting and you need the first-three-minutes verification pattern.
- A session felt wrong and you are deciding whether to keep the data.
- Do not use this when the problem is that qualified people are not showing up at all — that is
  a sequence problem, use `participant-comms`.

## Gather first

1. The qualify key from `screener-builder`, including the trap item.
2. Recruiting source, since `~~recruiting panel` fraud differs from customer-list fraud.
3. Whether the roster is pre-session, mid-session, or complete, and how many sessions remain.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Screen the screener responses first.** Flag on: the fictional trap item; completion under
   half the median time; straight-lining a grid; the maximum option on every frequency question;
   an open-ended that is generic, grammatically uniform, or answers a question you did not ask;
   an open-ended that repeats the product's own marketing language; near-identical open-ended
   text across two respondents; a disposable email domain; a stated geography that contradicts
   the timezone they booked in.
2. **Treat maximal answers as a stronger signal than minimal ones.** Someone genuinely in the
   behavior says "maybe eight, I lost count." A fake says "more than 20" on every single
   question.
3. **Watch the scheduling behavior.** Flag on: booking the first available slot within 60
   seconds; rescheduling more than twice; asking about the incentive before asking anything about
   the study; requesting payment in advance or to a different email than the one that qualified;
   joining under a different name than booked.
4. **Verify in the first three minutes, with three moves.** Do this before the guide starts,
   framed as warm-up, never as interrogation:
   - **Ask for a concrete last instance.** "Tell me about the most recent time you did that, what
     day was it and what were you doing right before?" Real behavior has texture, ordering, and
     irrelevant detail. Fabricated behavior stays abstract and stays on topic.
   - **Ask for a small number they would know and could not guess.** Build it from the core
     behavior described in the product context: a count from the last week, plus the companion
     count that only someone actually doing it would carry. A real participant answers fast and
     roughly, often with feeling about the ratio. A fake produces a tidy number with no affect.
   - **Ask them to show or describe something only a user would have.** A share of their own
     workspace, or where a specific step lives. If screen sharing is not consented, ask what they
     saw last time and what they did next.
5. **Score, do not agonize.** Two or more flags from steps 1 and 3 combined, or one failed
   verification move in step 4, means end the session.
6. **Exit gracefully, on script.** Do not accuse, explain the detection, or negotiate. Say:
   "Thank you, that is really helpful. It turns out this study needs a slightly different set of
   experiences than I described, so I will stop here and let you go early. You will be paid the
   full amount today and nothing further is needed from you." Then end the call and pay in full,
   per `incentives-and-consent`. Never withhold payment as punishment: it invites a dispute, and
   you may be wrong.
7. **Rule on the data with a single rule.** If the session ended on a fraud flag, discard the
   whole transcript, including the parts that seemed good. Do not salvage quotes: a fluent fake
   produces the most quotable lines in the study, which is why partial retention is how bad data
   survives. If a flag surfaced only after a completed session, quarantine the transcript in
   `~~research repository`, exclude it from synthesis, and note the exclusion in the report's
   method section as a count, not a name.
8. **Report the rate, not the incident.** Log flagged and discarded counts per source in
   `~~project tracker`. A source above a 15% flag rate gets replaced, not screened harder.
9. **Over-recruit rather than tighten mid-study.** Changing criteria partway makes cells
   incomparable. Add participants from the same frame at the end instead.

## Output

- **Pre-session flag table** — participant ID, flags triggered, verdict (invite / hold / drop).
- **First-three-minutes script** — the three verification moves, written as they will be spoken.
- **Scoring rule** — the flag thresholds, stated once.
- **Graceful exit script** — verbatim, plus the payment instruction.
- **Data ruling** — per affected session: discard, quarantine, or keep, with the reason.
- **Source quality line** — flag rate per recruiting source and a keep/replace call.

## Quality bar

- Every flag is an observable signal, not an impression of the person.
- The verification moves ask for texture and numbers, never for identity documents.
- The exit script accuses nobody, explains nothing, and confirms full payment.
- The keep-or-discard rule is applied without exception, including to quotable sessions.
- Exclusions are counted in the method section of the eventual report.
- Every participant-facing line passes the language and tone rules in the product context.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If any participant was discarded, invoke `screener-builder` now to tighten the criteria, then
  `participant-comms` to book the replacements.
- If more than one in four from a single source was flagged, invoke `sampling-plan` instead and
  change the frame rather than replacing individuals.
- Invoke `session-debrief` next unless the user redirects, carrying the exclusion count so it
  reaches the method section written by `pyramid-report`.
- Stop here if the roster is clean and no session has run yet.
