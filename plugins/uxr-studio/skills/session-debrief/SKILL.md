---
name: session-debrief
description: >
  This skill should be used when the user asks to "debrief a session", "write up the
  interview I just did", "capture notes after a research session", "log what happened in
  that call", or needs to record a session before memory decays. Produces a fixed-template
  debrief with observations separated from interpretation and a running code tally.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Session Debrief

The thirty minutes after a session is the highest-value half hour in the whole study.
Unwritten memory does not simply fade, it decays into whatever confirms the team's prior, so
the debrief must be written before the next session and never batched at the end of the
round. A study of twelve sessions debriefed on the last day is a study of the last two
sessions plus a confident reconstruction of ten.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A moderated session just ended and nothing has been written down yet.
- A notetaker's raw notes or a `~~transcription tool` transcript needs turning into a
  structured record.
- A study is mid-field and the team needs to see what is emerging before the last session.
- Do not use this to build themes across sessions, use `affinity-synthesis`. Do not use it to
  code a large corpus, use `thematic-coding`.

## Gather first

1. Which participant and which segment from the product context, plus session number in the
   round.
2. Raw material available: transcript, notes, recording, or the moderator's memory only.
3. The research question the study is answering.
4. The running code tally from prior sessions in this round, if one exists.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Clock it.** State when the session ended and when the debrief is being written. If the
   gap exceeds four hours, mark the debrief `reconstructed` and treat every claim in it as
   weaker evidence than a same-hour debrief.
2. **Fill the fixed template, in this order, no substitutions.** The order matters because
   surprise is the fastest-decaying signal.
   - **What surprised me.** Anything that violated the moderator's expectation going in. If
     the answer is "nothing", write that. Repeated "nothing" across sessions means either
     saturation or a guide that only asks what is already known.
   - **What contradicted a prior session.** Name the participant ID contradicted and what
     they said instead. This field is mandatory from session two onward. Teams reliably
     record agreement and silently drop disagreement.
   - **Strongest verbatim.** One or two quotes, transcribed exactly, with a timestamp. Do not
     clean up grammar. Do not paraphrase into the team's vocabulary.
   - **What I would change in the guide.** Concrete question edits, not "go deeper". If a
     change is made, note which session it takes effect from so later analysis knows the
     instrument moved.
3. **Separate observation from interpretation, mechanically.** Write every line in one of two
   labelled columns or prefixes: `OBS` for what was said or done, `INT` for what it might
   mean. "Participant hesitated for eleven seconds, then re-read the label" is OBS. "They did
   not trust the number" is INT. Never let an INT line sit unlabelled, and never let an INT
   line exist without at least one OBS line under it. If an interpretation has no observation
   beneath it, delete it.
4. **Update the running code tally.** Maintain a single table across the round: code name,
   one-line definition, count of sessions it has appeared in, session IDs. Add a code only
   when a new observation does not fit an existing one. Track new codes per session. When two
   consecutive sessions add zero new codes, flag that saturation is approaching, and when
   three do, recommend stopping or re-sampling into a segment from the product context that
   has not yet been covered. Count sessions, never mentions.
5. **Flag sampling drift.** Note whether this participant actually matched the intended
   segment and quota. Recruiting slippage is visible in the debrief and invisible later.

## Output

```markdown
# Debrief — [Participant ID] — [segment] — session N of M
Session ended: [time] · Debrief written: [time] · Status: same-hour | reconstructed

## What surprised me
## What contradicted a prior session
## Strongest verbatim
> "[exact quote]" — [PID], [timestamp]

## What I would change in the guide
## Observations and interpretations
OBS: ...
  INT: ...

## Running code tally
| Code | Definition | Sessions | Session IDs |
New codes this session: N · Consecutive sessions with zero new codes: N

## Sampling note
```

## Quality bar

- Every `INT` line has at least one `OBS` line supporting it.
- Quotes are exact, timestamped, and in the participant's own words.
- The contradiction field is filled or explicitly marked "none found", never left blank.
- The code tally counts sessions, not mentions, and carries forward from prior debriefs.
- No product jargon appears in a quote or an OBS line unless the participant used it.
- Guide changes are written as specific replacement wording with an effective session number.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `affinity-synthesis` next unless the user redirects, once every session in the round has
  a debrief.
- If the corpus is too large to cluster by hand, invoke `thematic-coding` instead.
- If the code tally or the contradiction field calls for a guide change, invoke
  `interview-guide-builder` before the next session.
- Invoke `research-repository-hygiene` to file the debrief and its running tally.
- Stop here if sessions remain in the round. Run this skill again after the next one and hold
  synthesis until the set is complete.
