---
name: journey-map-builder
description: >
  This skill should be used when the user asks to "build a journey map", "map the user
  journey", "show the end-to-end experience", "where do we lose people", or needs an
  evidence-backed map of the experience. Produces a journey with evidence per stage, assumed
  stages flagged, and a ranked opportunity list rather than a poster.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Journey Map Builder

Map the journey the user actually has, not the journey the product offers. That means including the stages before anyone arrives, the work done in other tools, the
detours through competitors, and the parts that happen offline. A map bounded by your own product cannot show you where you lose people, because the loss
happens in the gaps it leaves out. The map is not the deliverable. A ranked opportunity list
is.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- Drop-off is known but not understood, and the team needs to see where the experience breaks.
- Multiple teams own different stages and nobody owns the seams between them.
- A persona exists and its experience over time needs laying out.
- Do not use this to describe who the users are, use `persona-builder`. Do not use it to
  quantify where users drop out of an instrumented flow, use `funnel-diagnostics`, then bring
  those numbers here as evidence per stage.

## Gather first

1. Whose journey: which persona or segment from the product context. One journey per segment,
   never a blended average user.
2. The start and end boundary, expressed as the user's goal, not the product's funnel.
3. Evidence available: sessions, `~~product analytics`, `~~support desk`, `~~review sites`.
4. The decision the map must inform.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Set boundaries from the user's goal.** Start where the user first recognizes the need, typically
   well before any product touchpoint, and end when their goal is met or abandoned. Note where the funnel in the product context is
   narrower than the journey. That gap is usually where the unexplained losses live.
2. **Define stages by the user's intent, not your screens.** Name each stage with what the
   user is trying to accomplish. A boundary is where their intent changes. Screens,
   features, and team ownership are not stages. Aim for five to nine stages.
3. **Include off-product stages explicitly.** Mark each stage with where it happens: in the
   product, in another tool, at a competitor, or offline. A map showing none is a map of the product, not the journey.
4. **Attach evidence to every stage.** Per stage, record what the user does, what they think,
   the strongest verbatim with a participant ID, and any behavioral number. Mark each stage `observed` or `assumed`, with assumed
   marked on the artifact itself, not in a footnote. An unmarked assumption becomes a fact
   within two meetings.
5. **Run two tracks under the stages.** Emotional track: the user's state, stated in their own
   words rather than in emoji or a smoothed curve. Effort track: how much work the stage costs
   them, as time, steps, or decisions. Keep them separate. Low effort with high
   frustration and high effort with high satisfaction are both real and both diagnostic, and a
   single blended "experience" line hides them.
6. **Identify the deciding moments.** Find the two or three stages where the outcome is
   determined: where people commit, where they quit, where trust is won or lost. The rest of
   the map is context for these. Connect any stage that maps onto a standing problem area in
   the product context by name.
7. **Convert to a ranked opportunity list.** For each deciding moment and each high-effort or
   high-frustration stage, write an opportunity as a sentence naming the user's blocked
   intent. Rank by evidence strength, population size from the `~~data warehouse`, and proximity to
   the decision the map informs. Say which are not worth pursuing and why. A map that ends as
   a poster has produced nothing.
8. **State the map's expiry.** Set a review date using the feedback-loop length in the product
   context, and note what product change would invalidate the map.

## Output

```markdown
# Journey — [persona or segment] — [goal boundary] — [date]
Evidence base: N sessions, behavioral sources, date range.

## Stage table
| Stage (user intent) | Where it happens | Doing | Thinking (verbatim + PID) | Emotion | Effort | Evidence | observed / ASSUMED |

## Deciding moments
The 2-3 stages that determine the outcome, and what tips each one.

## Ranked opportunities
| # | Opportunity (blocked intent) | Stage | Evidence strength | Population affected | Decision it informs |

## Not pursuing
## Assumptions to validate
Every ASSUMED stage, and the cheapest study that would confirm it.

## Expires
Review date and what would invalidate this map.
```

## Quality bar

- Stages are named by user intent and at least one stage happens outside the product.
- Every stage is marked observed or assumed, with assumed marked visibly on the artifact.
- Emotional and effort tracks are separate and evidenced, not drawn from intuition.
- Two or three deciding moments are identified and justified.
- The deliverable ends in a ranked opportunity list with a "not pursuing" section.
- The journey covers one segment, not a blended average user.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now on the ranked opportunities. It is a mandatory gate: a map that
  fails it is a poster.
- Invoke `opportunity-backlog` next unless the user redirects, so surviving opportunities
  accumulate rather than expire.
- If an opportunity has an owning team with capacity, invoke `insight-to-spec` for that one
  instead.
- If any stage is marked ASSUMED, invoke `research-roadmap` to schedule the study that confirms
  it.
- If the deciding moments sit on an instrumented flow, invoke `funnel-diagnostics` to size the
  drop.
