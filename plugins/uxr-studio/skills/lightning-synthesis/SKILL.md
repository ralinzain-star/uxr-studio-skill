---
name: lightning-synthesis
description: >
  This skill should be used when the user asks for "a quick read on the research", "what does
  the data say by end of day", "we ship Thursday and need an answer", "rapid synthesis", or
  needs a defensible answer in about two hours. Produces a timeboxed synthesis carrying an
  explicit confidence level and an expiry date.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Lightning Synthesis

Two-hour synthesis is a legitimate method, not a failure to do the real one. A decision that
gets made Thursday either gets made with two hours of evidence or with none. What makes it
legitimate is the label: it ships with a stated confidence level and an expiry date so it can
never be quoted six months from now as though it were a full study. Unlabelled fast work is
how a hallway opinion acquires a citation.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A decision has a date inside the week and full synthesis cannot land before it.
- Sessions have been run and debriefed but nothing is analyzed.
- A question can be answered well enough to change the decision, even if not well enough to
  settle the topic.
- Do not use this when the decision is expensive to reverse, when the result will set strategy
  or pricing, or when there is time. Use `affinity-synthesis`, or `thematic-coding` for a
  large corpus.

## Gather first

1. The decision, its owner, and the exact date it gets made.
2. What evidence already exists: debriefs, transcripts, `~~product analytics`, prior studies
   in the `~~research repository`.
3. What the team currently believes and intends to do absent new evidence.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

Run the clock out loud. Announce each timebox and stop when it ends, even mid-thought.

1. **Frame, 10 minutes.** Write the decision as a single sentence and the default action the
   team takes if research says nothing. Everything downstream exists only to confirm or change
   that default. Discard any question that cannot change it.
2. **Pull, 20 minutes.** Gather only sources already in hand. Check the `~~research
   repository` for a prior answer first, a study that already exists beats a fast one. Do not
   start new collection.
3. **Skim for disconfirmers first, 30 minutes.** Read the corpus once, hunting specifically for
   evidence against the default action, then once for evidence supporting it. In that order.
   Reversing the order produces a confirmation exercise every time.
4. **Cluster to three claims maximum, 30 minutes.** Cluster only well enough to state at most
   three claims, each as a full sentence with a subject and a verb. Count sources per claim as
   you go. Resist a fourth, a two-hour synthesis that produces seven findings has produced
   none.
5. **Write, 20 minutes.** Claim, sources, consequence, confidence. Use the `insight-writer`
   form for each claim, compressed.
6. **Confidence and expiry, 10 minutes.** Assign one level per claim and set the expiry.

**Deliberately skip:** a second coder, full transcript reads, verbatim-perfect quote
transcription, a formal codebook, segment-by-segment breakdowns beyond the one segment that
drives the decision, polished slides, and any theme that does not touch the decision.

**Never skip, at any speed:** the disconfirming pass, source counts per claim, checking
whether the sample hits the sampling trap named in the product context, the confidence
statement, and the expiry date. These four are what separate a fast answer from a guess, and
removing any of them removes the right to call the output research.

7. **Name what would change the answer.** One line per claim, stating the cheapest evidence
   that would overturn it and roughly what it would cost to get.

## Output

```markdown
# Lightning synthesis — [decision] — [date]
LIGHTNING SYNTHESIS · 2 hours · Confidence: [level] · Expires: [date]
Not a full study. Do not cite after the expiry date without re-running.

## Decision and default
The decision, its owner, its date, and what happens if research says nothing.

## Claims (max 3)
### C1. [Full-sentence claim]
Sources: N of M · segments · Consequence if true: ...
Confidence: high | medium | low · What would overturn it: ...

## Evidence against
What was found arguing the other way, or that nothing was.

## What was skipped
The specific shortcuts taken and what they cost.

## Sampling caveat
Geography, segment, or recruitment limits that bound the claims.
```

Set the expiry to one feedback-loop length as given in the funnel section of the product
context, and shorter where the funnel stage in question is moving. Put the banner line in the title, the first slide, and
the `~~chat` message, not only in the document body. The banner is the deliverable.

## Quality bar

- The header banner names the method, the confidence, and the expiry, and appears before any
  finding.
- No more than three claims, each a sentence that could be wrong.
- Every claim carries a source count over a stated denominator.
- The disconfirming pass is written up, including when it found nothing.
- The skipped list is explicit and honest about what the shortcuts cost.
- Each claim names the evidence that would overturn it.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now on all three claims. It is a mandatory gate: a fast claim that
  changes nothing is worse than none.
- Invoke `topline-writer` next unless the user redirects, carrying the banner, confidence and
  expiry into the message.
- If any claim is low confidence, invoke `research-roadmap` to queue the full study.
- Invoke `impact-tracker` now to log the decision this fed. It is a mandatory gate: the claim
  expires and someone must check whether it held.
