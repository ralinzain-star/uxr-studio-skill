---
name: insight-writer
description: >
  This skill should be used when the user asks to "write up this insight", "turn this finding
  into an insight", "how do I phrase what we learned", "write the key takeaway", or needs a
  single research finding stated so it can change a decision. Produces a three-part insight:
  claim, counted evidence, and consequence, with confidence and a falsifier.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Insight Writer

A finding is what happened. An insight is what it means and what follows from it. "Nine of
twelve participants re-read the result before acting" is a finding. An insight says why that
happens and what the team must do differently if it is true. Write every insight in three
fixed parts and refuse to ship one that could not turn out to be wrong.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A theme has survived synthesis and needs stating so a team can act on it.
- A finding keeps getting nodded at in readouts and never changing anything.
- An entry is going into the `~~research repository` and must stand alone years later.
- Do not use this to build the themes in the first place, use `affinity-synthesis` or
  `thematic-coding`. Do not use it to structure a whole report, use `pyramid-report`. Do not
  use it to test whether a report's findings matter, use `so-what-checker`.

## Gather first

1. The theme or finding, with its supporting evidence and source IDs.
2. The denominator: how many participants, of what total, from which segments in the product
   context.
3. The decision this is meant to inform, from the decisions list in the product context.
4. Any quantitative evidence that agrees or disagrees.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the claim as one sentence with a mechanism.** Subject, verb, and a because. State
   what people do and why, not what they said they want. The claim must be the kind of thing
   that could have come out the other way. If a reasonable colleague could not have predicted
   the opposite, it is not a claim, it is a description.
2. **Apply the ban list.** Reject any insight that is topic-shaped, wish-shaped, or
   tautological. "Users want better onboarding" fails three ways: it names a topic, it
   restates a desire, and it could not be false. Replace the pattern "users want X" with "when
   [situation], people do [behavior], because [mechanism], which costs them [consequence]".
   Also ban insights whose only content is a feature request, the request is data, the insight
   is why it was requested.
3. **Attach evidence with counts.** Sources over denominator, segment breakdown, and two or
   three exact verbatims with IDs. Count participants, never mentions. Name any evidence from
   `~~product analytics` or the `~~data warehouse` that supports or complicates it. Where the
   sample brushes the sampling trap named in the product context, say so in the evidence
   block, not in a footnote.
   **Hard stop: if the source count and the denominator are not known, do not continue.**
   Ask for them with AskUserQuestion and wait. Never infer a count, never write "TBD" or
   "several participants", and never proceed assuming the numbers arrive later. An insight
   whose evidence is uncounted cannot be weighed against any other, which is the whole reason
   for writing it down. The only exception is an explicitly unattended run, in which case emit
   the verdict `Blocked: evidence count and denominator unknown` and nothing else.
4. **Write the consequence if true.** Name what changes: which decision from the product
   context is affected, which direction it moves, and what the team should stop doing. An
   insight with no consequence is trivia with sources. If the honest consequence is "nothing
   changes", say that explicitly, it is a real and useful result.
5. **Assign confidence with reasons.** High, medium, or low, followed by the reason: source
   count, segment coverage, whether behavior or self-report carried it, and whether a second
   method agreed. Never state confidence as a number without a method behind it.
6. **Name the falsifier.** One sentence stating the specific observation that would overturn
   this insight, and where it would come from. An insight without a falsifier cannot be
   retired and will outlive its truth.
7. **Set a review date.** Use the feedback-loop length in the product context to choose it.
8. **Title it as the claim.** The title is the claim in short form, never the topic. A
   repository full of topic titles is a filing system nobody searches twice.

## Output

```markdown
## [Title = the claim, shortened]

**Claim.** [One sentence: behavior, situation, mechanism.]

**Evidence.** N of M participants · segments: [...] · study: [...]
> "[verbatim]" — [PID]
Corroborating or conflicting data: [...]
Sampling limits: [...]

**Consequence if true.** Decision affected, direction, what to stop doing.

**Confidence.** [high | medium | low] because [...]
**What would overturn this.** [...]
**Review by.** [date]
```

## Quality bar

- The claim is one sentence containing a mechanism and could have come out the other way.
- The title states the claim, not the topic.
- Evidence is counted as sources over a denominator with segments named.
- The consequence names a specific decision and a direction, or states plainly that nothing
  changes.
- Confidence is stated with its reason, and a falsifier and review date are present.
- No sentence of the form "users want", "users need", or "users struggle with" survives.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now, before this insight is shown to anyone. It is a mandatory gate:
  an insight that survives it is the only kind worth filing.
- Invoke `pyramid-report` next unless the user redirects, or `topline-writer` if the decision
  lands within a day.
- If a team owns the consequence, invoke `insight-to-spec`; if nobody does, invoke
  `opportunity-backlog` instead.
- Invoke `research-repository-hygiene` next so the review date is honored rather than ignored.
