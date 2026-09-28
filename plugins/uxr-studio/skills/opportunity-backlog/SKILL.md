---
name: opportunity-backlog
description: >
  This skill should be used when the user asks to "keep a list of what we know", "what should
  we work on next", "add this to the research backlog", or needs research findings to accumulate
  instead of expiring. Produces and maintains a ranked, decaying list of evidenced opportunities
  with strength, size, and confidence per entry.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# Opportunity Backlog

Make the backlog the team's primary research artifact and demote the one-off report to a source
document. Reports are read once and forgotten by the next planning cycle. A ranked list of
evidenced opportunities compounds, gets consulted when money is being allocated, and makes the
absence of evidence visible. Every entry carries its evidence strength, its size, and its
confidence, and every entry decays on a schedule until someone revalidates it.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A study finished and produced findings nobody is resourced to act on yet.
- Planning is starting and someone asks what research already knows.
- Findings are scattered across reports and the same question keeps getting re-asked.
- Do not use this when an insight already has an owner and a team with capacity. Send it to
  `insight-to-spec` and link the backlog entry to the ticket.

## Gather first

1. The existing backlog, wherever it lives, and the date each entry was last touched.
2. New insights to add, with their evidence, from `insight-writer` or `triangulation`.
3. Available sizing data in `~~product analytics` or `~~data warehouse`.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write each entry as an opportunity, not a solution or a study.** Shape: "<Segment> cannot
   or does not <behaviour> at <step>, which costs <consequence>." Anything phrased as a feature
   is rewritten or rejected.
2. **Score every entry on three axes and never collapse them into one number.** A single priority
   score hides which of the three is weak, which is exactly the thing a reader needs to know.
   - **Evidence strength:** strong (triangulated across two or more independent sources),
     moderate (one method, adequate sample, segment-clean), weak (one method, thin or skewed
     sample), anecdotal (unreplicated).
   - **Size:** users affected, frequency, and the revenue or funnel step at risk, each with its
     source and denominator. Unsized entries are allowed but are marked and always rank below
     sized ones of equal strength.
   - **Confidence:** how sure you are that acting on it moves the size, stated high, medium, low,
     with the reason in one clause.
3. **Rank by size times confidence, then break ties with evidence strength.** Sort within the
   known problem areas named in the product context so the backlog maps onto the standing agenda
   rather than competing with it. Publish the ranking rule at the top of the list so it can be
   argued with.
4. **Merge duplicates aggressively, and merge on mechanism rather than on symptom.** Two entries
   describing the same underlying behaviour in different surfaces merge into one with two
   evidence trails and a combined size. Two entries sharing a symptom but different mechanisms
   stay separate. When merging, keep the earliest first-observed date, sum the size only where
   the populations are genuinely distinct, and never raise evidence strength just because two
   weak entries agree. Record the merge and keep the old ids as redirects.
5. **Apply the decay rule.** Every entry carries a last-validated date and a review interval set
   by evidence strength: strong 12 months, moderate 6 months, weak 3 months. Past the interval,
   the entry is marked stale, drops one confidence level, and cannot be cited in a planning
   conversation until revalidated. Revalidation is a check against current data or a small study,
   not a re-read of the old report. Set the interval shorter than the product's feedback-loop
   length where the underlying behaviour is known to shift with the market or the season.
6. **Retire, do not delete.** Entries that were shipped against, disproven, or gone stale twice
   move to a retired section with a one-line reason and the date. The graveyard is what stops the
   same idea re-entering every year.
7. **Keep it one list.** Per-team backlogs recreate the problem the backlog was meant to fix.
   Filter one list by team instead.

## Output

Maintain in `~~research repository`, one row per entry:

```
id | opportunity statement | segment | problem area | evidence strength | size (+source)
   | confidence (+reason) | rank | first observed | last validated | review due | status
   | evidence links | owner if any | linked tickets in ~~project tracker
```

Plus a header stating the ranking rule and the decay intervals, a stale section, and a retired
section with reasons.

## Quality bar

- Every entry is a behaviour and a cost, never a feature.
- Strength, size, and confidence are separate fields and none is blank or merged.
- The ranking rule is written at the top and the order follows it.
- Every entry has a last-validated date and a review-due date.
- Stale entries are visibly marked and downgraded, not silently carried.
- Merges are recorded with old ids retained, and no merge inflated evidence strength.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-roadmap` next unless the user redirects, taking the top ranked entries into
  the quarter, then `research-repository-hygiene` so review-due dates are enforced.
- If an entry has an owning team with capacity, invoke `insight-to-spec` for it instead of
  leaving it ranked.
- If an entry is stale, invoke `prior-evidence-check` to revalidate it against current data
  before it is cited.
- Stop here if nothing is owned and no planning cycle is open.
