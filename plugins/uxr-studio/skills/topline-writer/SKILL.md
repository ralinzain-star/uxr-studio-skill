---
name: topline-writer
description: >
  This skill should be used when the user asks for "early findings", "a quick readout", "what
  are we seeing so far", or needs to get something out to stakeholders within a day of the last
  session. Produces a short, fixed-format topline with a stated confidence level and an explicit
  note on what full synthesis could change.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# Topline Writer

Ship a topline within 24 hours of the last session. A confident partial read with a stated
confidence level beats a complete read that arrives after the decision, because a report that
lands late is not a slower report, it is an unread one. The topline is not a teaser for the
report. It is the deliverable that has to carry the decision if nothing else arrives in time.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- The last session is done or the field is more than half complete and stakeholders are waiting.
- A decision, sprint planning, or roadmap meeting falls before full synthesis can finish.
- Someone asks in `~~chat` what you are seeing and the honest answer is "a lot, unsorted".
- Do not use this when synthesis is complete and the decision date is comfortably out. Write the
  full `pyramid-report` instead.

## Gather first

1. The decision the topline has to reach, and the date it gets made.
2. Sessions completed against sessions planned, and the segment mix so far.
3. Debrief notes from `session-debrief` and any patterns from `lightning-synthesis`.
4. What is still in field or still uncoded.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Set the clock first.** Write the topline against the decision date, not against synthesis
   readiness. If the last session was today, the topline goes out today or tomorrow morning.
2. **Cap it at one screen.** Roughly 250 to 400 words, no appendix, no slides. If it needs
   scrolling, it is the report arriving early and badly.
3. **Lead with the strongest read, stated as a claim with a confidence level.** Use three levels
   only: high (seen in most sessions across more than one segment, and the pattern held after you
   looked for the opposite), medium (a clear pattern in one segment or a majority in a thin
   sample), low (suggestive, would not bet on it). Never use percentages on a qualitative topline.
   Counts out of sessions run are fine and preferred.
4. **Decide what is safe to say early.** Safe: behaviours you watched happen, blockers that
   stopped a task, language participants used unprompted, anything that repeated across segments.
   Unsafe and must wait: prevalence claims, segment comparisons drawn from fewer than the planned
   cells, causal explanations, anything resting on a single vivid participant, and any number that
   would be quoted back as a statistic.
5. **Say what is not yet known and name the date it will be.** Every deferral gets a date.
   A topline with open items and no synthesis date reads as an excuse.
6. **Include the change line, verbatim in spirit:** one sentence naming what full synthesis could
   overturn, and what it will add. Example shape: "Full synthesis may reorder these by prevalence
   and will add the segment split. It is unlikely to remove the first item."
7. **Do not recommend yet unless the decision forces it.** If it does, mark the recommendation as
   provisional, state the confidence, and say what would reverse it.
8. **Respect the sampling trap.** State the geography and segment composition of the sessions run
   so far against the segment table in the product context. Early samples skew toward whoever
   responded fastest, which is rarely the segment the decision is about.

## Output

```
Topline: <study name>, as of <date>
One-line read        the strongest claim, with confidence level
What we saw          3-5 bullets, each a behaviour or blocker + count out of sessions run
Sample so far        n of planned, segment mix, geography, known skew
Too early to say     2-4 bullets, each with the date it will be answered
What changes at synthesis   one sentence on what could be overturned or added
Next                 synthesis date, full report date, who to ask in the meantime
```

Send it in `~~chat` and file it in `~~research repository` under the study, so the topline and the
eventual report sit together.

## Quality bar

- Fits on one screen and carries no appendix.
- Every claim has a confidence level and a count out of sessions run.
- No percentage appears on a qualitative sample.
- Every deferred item has a date.
- The change line is present and says what could be overturned, not just what will be added.
- Sample composition and geography are stated, with the skew named.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now, before the topline is sent. It is a mandatory gate: an early
  read travels further than the report and gets quoted longer.
- Invoke `affinity-synthesis` next for the full pass unless the user redirects, or
  `thematic-coding` instead when the corpus is too large to cluster by hand.
- Invoke `pyramid-report` after the full pass, then `so-what-checker` again on the report.
- Stop here if fieldwork is still running. Re-issue the topline after the next batch rather than
  synthesizing on a partial set.
