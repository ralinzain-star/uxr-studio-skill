---
name: contextual-inquiry
description: >
  This skill should be used when the user asks to "do a contextual inquiry", "shadow a user",
  "watch people work", "do a site visit", or needs to understand a task in its real setting
  rather than in a lab. Produces a visit plan: recruiting frame, observation protocol, a
  what-to-record checklist, the interruption rules, and a remote screen-share variant.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Contextual Inquiry

Watch first, ask afterwards. The interruption that feels most natural to the researcher is the
one that destroys the behavior being observed, because the moment you ask a person to explain,
they stop working and start performing. Structure the visit so the first stretch is silent
observation and the questions are held until a natural seam.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- The task spans tools you do not own, so your own data cannot show the whole workflow. Build
  the example from the product context: take the core loop named in the features section and
  list the outside tools a real session runs through on either side of it.
- Reported behavior and logged behavior disagree and you need to know which is lying.
- You suspect workarounds. People do not report them because they stopped noticing them.
- Do not use this when you need the behavior tracked over weeks rather than watched once. Use
  `diary-study-design`. Do not use it to evaluate a specific flow against tasks you set. Use
  `usability-test-plan`.

## Gather first

1. The work being observed, named as the participant would name it, not as the product does.
2. Where and when it naturally happens, and whether a physical visit is possible.
3. The decision this feeds, so the checklist can be weighted toward it.
4. Any recording, privacy, or workplace constraints on the setting.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Open with the master-apprentice framing, out loud, in the first two minutes.** Say: "You
   are the expert and I am the apprentice. Work the way you normally would. I will mostly stay
   quiet, and I will save my questions for when you pause." That move buys permission to be
   silent, which is the hardest thing to do in someone's workspace.
2. **Schedule the visit around real work, never around your calendar.** Ask them to pick a time
   when they would be doing this anyway. A demonstration of the work is not the work.
3. **Stay silent for the first 15 minutes.** Take notes, do not speak. Most of what you came
   for happens before the participant has adjusted to you being there.
4. **Record five things, in this order of value.** Workarounds first: any patch the person has
   built around the tool, whether a spreadsheet, a naming convention, a second browser window, a
   note on paper, or an order of operations nobody designed. A workaround is the highest-value
   observation this method offers, because it is a specification for a missing feature written
   by the person who needs it. Then the artifacts they produce or consume, including ones that
   never touch your product. Then every other tool open alongside yours and what moves between
   them. Then interruptions, their cause, and the cost to resume. Then the environment: noise,
   screen size, device, who else is present, how much time they actually had.
5. **Hold questions until a natural seam.** A seam is a completed subtask, a save, a send, a
   pause to think. Never interrupt mid-action to ask why. Keep a running parking list and clear
   it at the seams.
6. **Ask about the action just taken, not about the category.** "You just renamed that file
   before uploading it. Walk me through that." Not "how do you usually manage files?"
7. **Do not correct, teach, or demo.** The moment you show the better way, the visit is over
   and you have bought a tutorial with your research budget.
8. **Reserve the last 15 minutes for a retrospective walkback.** Summarize the sequence you
   observed and ask them to correct it. Their corrections are the highest-signal part of the
   transcript.
9. **Run the remote variant by adapting, not downgrading.** Ask for a full desktop share, not
   one window, so you can see the other tools. Have them mute your video and treat you as absent
   during the silent stretch. Accept that you lose the room: paper, phone, other people,
   physical interruptions. Compensate by asking at the end what happened off-screen and what
   they used that you could not see. State the limitation in the report.
10. **Write field notes within the hour.** Context memory decays faster than interview memory
    because most of it was never verbalized.

## Output

- **Visit plan** — who, where, when, what work, how long, remote or in person.
- **Consent and recording notes** — what is recorded, what is not, what happens to artifacts.
- **Opening script** — the master-apprentice framing, verbatim.
- **Observation checklist** — the five record categories as a note-taking template.
- **Interruption rules** — what counts as a seam, and the parking-list mechanic.
- **Walkback prompts** — the closing 15 minutes.
- **Field note template** — action sequence, artifacts, tools open, workarounds, interruptions,
  environment, open questions.
- **Remote variant** — the adaptations and the stated blind spots.
- **Assumptions** — anything inferred from the product context.

## Quality bar

- The plan schedules real work at the participant's time, not a demonstration at yours.
- A silent opening stretch is written into the protocol with a duration.
- Workarounds are named as the primary target, with prompts for surfacing them at the walkback.
- The checklist captures tools open alongside yours and artifacts that never touch your product.
- No prompt asks the participant to explain a category rather than an action just taken.
- If remote, the blind spots are listed and carried into the report.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: a visit records a workplace and third
  parties, so no visit is booked until it clears.
- Invoke `incentives-and-consent` next unless the user redirects, then `participant-comms` to
  schedule around real work.
- Invoke `session-debrief` next unless the user redirects, within an hour of each visit.
- Invoke `affinity-synthesis` next unless the user redirects, once three or more visits are
  debriefed.
- If confirmed workarounds are not being scoped now, invoke `opportunity-backlog` to hold them
  with their evidence.
