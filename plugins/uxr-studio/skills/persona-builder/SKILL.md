---
name: persona-builder
description: >
  This skill should be used when the user asks to "build personas", "who are our users",
  "create user archetypes", "update the personas", or needs behavioral archetypes that change
  product decisions. Produces at most four evidence-traced personas built on behavior and
  goals, each with a named decision it changes and a kill criterion.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Persona Builder

Personas built on demographics are decoration. Age, job title, and a stock photo have never
changed a product decision. Build them on behavior, goals, and the specific decision each one
changes. Cap the set at four, because a fifth persona is how a team stops using any of them,
and give every persona a kill criterion so it can be retired when it stops predicting rather
than living forever on a wall.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Repeated studies keep surfacing the same few behavioral patterns and the team needs shared
  shorthand.
- Teams are arguing about "the user" as a single undifferentiated person.
- Existing personas are demographic, aspirational, or unused, and need rebuilding.
- Do not use this to describe a sequence of experience over time, use `journey-map-builder`.
  Do not use it to find statistical clusters in behavioral data, use `segmentation-analysis`,
  then bring those clusters here.

## Gather first

1. The evidence base: which studies, how many participants, which behavioral sources from the
   `~~data warehouse` or `~~product analytics`.
2. Which decisions the personas must change, from the decisions list in the product context.
3. Whether a statistical segmentation already exists.
4. Who will use them and in what artifact.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Start from the segments already named in the product context**, then test each one
   against evidence rather than adopting it. A segment that exists in the business's language
   but does not show a distinct behavior pattern is not a persona.
2. **Differentiate on behavior, not attributes.** The axes must be things people do: frequency
   of the core action, what triggers them to start, what they do when the product fails them,
   what they abandon, and what outcome they are actually pursuing. Demographics may appear as a
   descriptive note only, never as a differentiator or a name.
3. **Require a distinct decision per persona.** If two candidate personas would lead the team
   to build the same thing, they are one persona. Merge them. This test alone usually takes
   seven candidates down to three.
4. **Cap at four. Prefer three.** A fifth real pattern is a research agenda item, not a
   persona.
5. **Fill the required fields for each.** Name and one-line behavioral definition. Trigger
   that starts them. Goal in their own words, quoted from evidence. What they do today,
   including workarounds outside the product. Where they fail or quit. What they will not
   tolerate. The decision this persona changes and in what direction. Evidence trace: study
   IDs, participant counts, and any behavioral metric that sizes the group. Kill criterion.
6. **Name by behavior, and test for caricature.** Use a behavioral label, not a first name and
   a stock photo. Apply the participant-facing language rules in the product context to every
   line: a persona must never imply the person is at fault or incompetent. Read each persona as though its subject were in the room.
7. **Refuse the aspirational persona.** Delete any persona that describes who the team wishes
   used the product but who does not appear in the evidence. It is the most common failure and
   the most expensive: it redirects roadmap toward an invented person. If the team wants that
   persona, it is a hypothesis, label it explicitly as unvalidated and put it on the research
   agenda rather than on the wall.
8. **Write the kill criterion.** A specific, observable condition that retires the persona:
   the behavior no longer separates groups, the group falls below a threshold share, or the
   decision it was built to inform has been made. Set a review date using the feedback-loop
   length in the product context.
9. **Size each persona.** Give a share of users or revenue from the `~~data warehouse`, or
   state plainly that it is unsized. An unsized persona will be assumed to be equal in size to
   the others, which is always false.

## Output

```markdown
# Personas — [date] — evidence base: N studies, M participants

## [Behavioral name]
**Definition.** One line, behavioral.
**Trigger.** **Goal (their words).** > "[verbatim]" — [PID]
**What they do today.** Including workarounds outside the product.
**Where they quit.** **What they will not tolerate.**
**Decision this changes.** [decision] → [direction]
**Size.** X% of [population] per `~~data warehouse`, or: unsized.
**Evidence.** Study IDs, participant counts, behavioral corroboration.
**Kill criterion.** **Review by.** [date]

## Patterns deliberately not made personas
## Unvalidated hypotheses (not personas)
```

## Quality bar

- Four personas or fewer, each differentiated by behavior rather than attributes.
- No two personas lead to the same product decision.
- Every persona names a decision, a direction, an evidence trace, and a kill criterion.
- Every persona is either sized or explicitly marked unsized.
- No persona could be read as blaming or caricaturing the person it describes.
- Any aspirational archetype is quarantined as an unvalidated hypothesis.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now on each persona's named decision. It is a mandatory gate: a
  persona that changes no decision is decoration.
- Invoke `journey-map-builder` next unless the user redirects, one journey per persona, then
  `research-repository-hygiene` so the kill criteria and review dates are enforced.
- If any persona is unsized, invoke `segmentation-analysis` to size it against behavioral
  clusters.
- Stop here if the set is a refresh and every kill criterion still holds.
