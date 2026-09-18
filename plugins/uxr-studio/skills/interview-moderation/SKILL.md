---
name: interview-moderation
description: >
  This skill should be used when the user asks "how do I run the interview", "moderate a
  research session", "the participant keeps giving me generalities", "what do I say when
  they ask me a question", or needs to run and take notes in a live 1:1 session. Produces
  a moderator runbook with scripts, probe rules, and a separated note-taking template.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Interview Moderation

The moderator's main job is silence. Most lost data in a research session is a follow-up
question asked two seconds too early, on top of an answer the participant had not finished
building. Treat every pause as the participant still working, not as dead air you must fill.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A guide exists and the first session is imminent.
- Sessions are producing thin, general answers and the guide is not the problem.
- Someone new to moderating is about to run a session alone.
- Do not use this to write the questions, use `interview-guide-builder`. Do not use it to
  analyze what came out, use `session-debrief` then `thematic-coding`.

## Gather first

1. The guide, or at least the episode the session is about.
2. Moderator experience level and whether a notetaker is present.
3. Recording and consent status.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Open with this script, in this order.** Who you are and that you did not build the thing.
   What the hour is for. No wrong answers, and you want the unflattering version. Recording,
   who sees it, how to stop. Incentive already sent and unconditional. Then: "Is there anything
   you would rather not talk about today?" Wait for a spoken yes on consent. Never imply the
   participant is at fault for their outcomes, and never promise an outcome.
2. **Run the four-second rule.** After the participant stops talking, count four seconds before
   you speak. The best material usually arrives in second three. Nod, stay neutral, and if the
   silence is unbearable write in your notes instead of talking.
3. **Probe with the shortest thing that works**, in escalating order: silence, then "mm-hm",
   then their own last three words repeated as a question, then "walk me through that", then
   a full question. Reaching for the full question first replaces their frame with yours.
4. **Convert generalities into episodes.** Present tense ("I usually just tweak it") means
   summarizing, not remembering. Respond: "Take me to the most recent time you did that. What
   day was it?" Then walk the clock. Never accept "usually", "typically" or "always" as an
   answer. Repeat as often as needed. It is not rude.
5. **Chase contradictions without prosecuting.** Put the contradiction on the record, not on
   the participant. "Earlier you said you trusted the score, and just now you said you rewrote
   it by hand anyway. Help me understand the gap." Attribute the confusion to yourself: "I may
   have this wrong." Never say "but you said". Both statements are usually true in different
   contexts, and the context is the finding.
6. **Deflect requests for the right answer.** "Is that what I was supposed to do?" Answering
   teaches them the product mid-session and destroys the rest of the data. Say: "I will answer
   at the end, I promise. Right now what you thought it meant is more useful than what it
   means." Park the question, answer it honestly after the last one, then stop taking data.
7. **Handle the three failure modes.** The rambler: "I want to make sure we get to X, can I
   pull us there?" The one-word answerer: switch from questions to requests, "show me" or
   "walk me through", and share screen. The pleaser praising everything: ask for the last time
   it annoyed them, and if nothing comes, ask what they would remove.
8. **Close in this order.** "What did I not ask that I should have?" Then their parked
   questions, answered. Then confirm the incentive and how follow-up works. Thank them without
   calling their answers good or helpful, which retroactively signals what you wanted.
9. **Keep notes in three columns, never blended.** Verbatim quote in quotation marks, exactly
   as said. Observed behavior, including pauses, backtracks and scrolling. Interpretation,
   marked as yours. Common mistake: logging "she was confused by the score" as an
   observation. The observation is "paused nine seconds, scrolled up twice, then said 'so is 62
   good'". Never let a paraphrase into the quote column.

## Output

```
# <Study name>: Moderator Runbook
Session length · segment · recording and consent status

## Opening script            <verbatim, read aloud>
## Probe ladder              <silence to full question>
## Generality repair lines
## Contradiction lines
## Deflection lines          <plus the parking lot for their questions>
## Failure-mode playbook     <rambler / one-word / pleaser>
## Closing script

## Note sheet (per session)
| Time | Verbatim quote | Observed behavior | Interpretation (mine) |
Parked questions to answer at the end:
```

## Quality bar

- The opening script is written to be read aloud and asks for spoken consent.
- Every probe in the ladder is shorter than the one after it, and silence is first.
- At least one scripted line exists for a generality, a contradiction, and a direct question
  from the participant.
- The note sheet keeps quotes, behavior, and interpretation in separate columns.
- No line in any script names a feature, explains the interface, or implies fault.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `session-debrief` next unless the user redirects, within an hour of the session while
  the observed behavior is still recoverable.
- If sessions remain, stop after the debrief and return here for the next one.
- If this was the last session, invoke `affinity-synthesis` on the note sheets, or
  `thematic-coding` instead when the corpus is too large to cluster by hand.
- If the debriefs show the guide is producing generalities rather than episodes, invoke
  `interview-guide-builder` to repair it before the next session.
