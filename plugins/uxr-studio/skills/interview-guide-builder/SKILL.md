---
name: interview-guide-builder
description: >
  This skill should be used when the user asks to "write an interview guide", "draft
  discovery questions", "what should I ask in the interviews", "build a research guide",
  or needs to turn a research question into a moderated session plan. Produces a timed
  discovery guide built on past episodes, with follow-up phrasing and banned question forms.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Interview Guide Builder

A discovery interview collects stories about specific past episodes, never opinions or
predictions. People are unreliable narrators of their own future and reliable narrators of
last Tuesday. "What did you do the last time" beats "what would you do" every time. Treat any
question containing "would" as a defect unless it sits in a deliberate concept probe at the
end.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A study needs a moderated 1:1 discovery or generative guide.
- Behavior is the unknown: how people do the thing today, what they patch around, where they
  give up.
- Do not use this to watch someone attempt tasks in an interface, use `usability-test-plan`.
  For reactions to an unbuilt idea use `concept-test-design`. For cancelled users use
  `churn-interview`.

## Gather first

1. The decision the study informs and the sharpened research question.
2. The segment being interviewed, as defined in the product context.
3. Session length and number of sessions.
4. Whether anything is shown at the end (concept, mock, pricing).

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Define the episode.** Name a bounded, recent unit of behavior: "the last time you <did the
   specific thing>", never the whole ongoing activity it sits inside. Take the bounded unit
   from the core loop in the features section of the product context.
2. **Build five acts, in this order.**
   - **Warm-up, 5 min.** Establish the last real episode and pin it in time. "Walk me through
     the last time you did that. When was it?" Get a date. Undated stories drift into
     generality.
   - **Episode walkthrough, 20 min.** Chronological only. Start before the product is involved
     and end after it. Ask what happened next, repeatedly. Never ask why until the sequence is
     complete.
   - **Workaround hunt, 10 min.** Ask what they did outside any tool: the spreadsheet, the
     saved doc they copy from, the friend who reviews things. Workarounds are the highest
     value data in the session. They are unmet need already paid for in effort.
   - **Switch or abandon moment, 8 min.** Find the last time they stopped, switched tools, or
     gave up. Ask what happened immediately before. The trigger is the finding.
   - **Desirability probe, 5 min, at the very end only.** Every forward-looking or reaction
     question goes here. Earlier placement contaminates every story that follows, because the
     participant now knows what you hope to hear.
3. **Write 10 to 14 primary questions, not 30.** A 60-minute session holds about 12 with real
   follow-up. A long guide yields shallow answers because the moderator races it.
4. **Attach follow-up phrases, not follow-up questions.** These open a story without steering:
   "Walk me through that." · "What happened right before?" · "Then what?" · "Say more about
   that." · "You said it was annoying. Annoying how?" · "When was the most recent time that
   happened?" · "What did you do instead?" · "Who else was involved?" · Repeat their last three
   words as a question. Silence.
5. **Ban these forms and ship the repair with each.** Common mistake: teams spot the leading
   question and soften the adjective instead of changing the tense.
   - Hypothetical: "Would you use a feature that did this for you?" to "Tell me about the last
     time software did something like that for you. What did you do with the result?"
   - Preference: "Do you prefer A or B?" to "Last time you got feedback here, what did you act
     on first?"
   - Frequency guess: "How often do you do this?" to "How many times did you do it last week?
     Let us list them."
   - Leading: "How frustrating was that?" to "What did you do after you saw it?"
   - Double-barrel: "Was it clear and useful?" to two questions, asked apart.
   - Feature-request: "What features do you want?" to "What part of this do you do by hand
     today?"
   - Product vocabulary: substitute the neutral verbs required by the language and tone rules in
     the product context. The product's own framing in a question biases the answer toward it.
   - Fault-implying: those same rules name the outcome a participant must never be made to feel
     responsible for. Ask what the process did, not what they failed to do.
6. **Tag each question with the assumption it attacks**, so the guide can be cut under time
   pressure by value rather than by order.

## Output

```
# <Study name>: Discovery Guide
Research question · segment · session length · what is shown, if anything

## Screening confirmation (1 min)
## Warm-up: the last episode (5 min)
## Episode walkthrough (20 min)   <numbered Qs, chronological>
## Workaround hunt (10 min)
## Switch / abandon moment (8 min)
## Desirability probe (5 min)     <the only forward-looking section>
## Close (2 min)
## Follow-up phrase bank
## Question-to-assumption map
```

## Quality bar

- Every question before the final section is about a dated, specific past event.
- No question uses "would", "could", "might", or "typically" outside the final section.
- No question names a product feature the participant has not already mentioned.
- Primary question count is 10 to 14 for a 60-minute session.
- Each question maps to a stated assumption, and none implies the participant is at fault.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now unless it has already cleared this study. It is a
  mandatory gate: no guide is fielded before the harm review.
- Invoke `interview-moderation` next unless the user redirects, so the first session has a
  runbook.
- If the desirability probe needs more than five minutes, invoke `concept-test-design` instead
  of enlarging the guide.
- Invoke `session-debrief` within an hour of each session, without waiting to be asked.
- Stop here if the study is not going to field.
