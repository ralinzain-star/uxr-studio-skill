---
name: segmentation-analysis
description: >
  This skill should be used when the user asks to "segment our users", "who are our user
  types", "cut the data by group", or needs to divide a population into groups that behave
  differently enough to deserve different decisions. Produces behavior-based segments with
  separation evidence, revenue sizing, and a kill criterion for each.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# Segmentation Analysis

Segment on behavior and needs, never on demographics. A segment is only real if it predicts
something you would act on differently, so the test for every proposed segment is the roadmap
test: if two segments get the same roadmap, they are one segment and you should merge them.
Age, country and role title are description, not segmentation.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A team is about to build for "our users" as if they were one population.
- Aggregate metrics are flat but you suspect two groups moving in opposite directions.
- An existing segmentation is being quoted in planning and nobody has checked it still predicts.
- Do not use this when you already know the groups and need to know which of them to serve first.
  Use `maxdiff-and-tradeoff` or `opportunity-backlog` instead.

## Gather first

1. The decision the segmentation will feed, and who will spend money differently because of it.
2. Behavioral and outcome data available in `~~data warehouse` and `~~product analytics`,
   including revenue per account and retention.
3. Whether an existing segmentation is in use, and what it is used for today.
4. Any qualitative evidence about distinct needs from `~~research repository`.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **State the decision first.** Write the sentence "we will do X differently for each segment"
   before looking at any data. Without it, you will produce a taxonomy nobody uses. If the
   sentence cannot be written, stop and say so.
2. **Choose the clustering basis from behavior and need, not identity.** Use variables the user
   generates: frequency and intensity of the core action, which features they reach, the job they
   arrived to do, tenure and lifecycle position, outcome achieved. Exclude demographics and
   firmographics from the basis entirely. Bring them back later only as a profiling layer that
   describes a segment after it exists, never as the thing that defines it.
   **Hard stop: if the actual behavioral and outcome data for these variables is not available,
   do not continue the segmentation.** Ask what exists in `~~data warehouse` and
   `~~product analytics` with AskUserQuestion and wait. Never infer the clusters, never write
   "TBD", and never proceed assuming the pull will happen later. Segments invented without data
   cannot be separation-tested or revenue-sized, which is the whole of the quality bar. The only
   exception is an explicitly unattended run, in which case emit the verdict `Blocked: no
   behavioral data to cluster on` and nothing else.
3. **Keep the basis small and mutually exclusive.** Four to eight variables. Drop anything that is
   a proxy for another (count of the core action and session count are one variable). Normalize for tenure so
   old accounts do not cluster together purely by having existed longer.
4. **Produce three to five segments, no more.** Beyond five, nobody can hold them and nobody
   staffs them. If the data suggests eight, you have found sub-behaviors, not segments.
5. **Validate separation before naming anything.** Three tests. First, between-segment variance on
   the basis variables should clearly exceed within-segment variance. Second, each segment must
   differ on at least one outcome that was not in the basis, such as retention, conversion or
   support contact, which proves the cut carries information rather than restating itself. Third,
   run the roadmap test: write the top intervention for each segment and merge any two that come
   out the same. Construct the worked example from the segment table in the product context: take
   two segments whose top interventions genuinely differ, say in one clause what each needs, then
   take a cut by a demographic or geographic variable and show it does not survive because the
   intervention on both sides is identical.
6. **Size each segment against revenue, not headcount.** Report accounts, share of revenue, share
   of retained revenue, and revenue per account. Headcount sizing promotes the loudest, cheapest
   group. Use the sampling trap named in the product context as the worked example: the
   population it concerns is large by headcount and small by revenue, so a headcount-sized
   segmentation promotes it to the biggest segment and points the roadmap away from the money.
   Say the geography of every segment out loud.
7. **Name segments for what they do, not who they are.** Use a behavioral verb phrase built from
   the basis variables, of the shape "does the core action in bulk and never edits the output" or
   "leaves and returns for a second attempt". Avoid personas with invented names,
   ages and stock photos, which invite caricature and let teams argue about a fiction instead of
   the behavior. Keep the name under six words and derivable from the basis variables.
8. **Write a kill criterion per segment.** State the measurement and threshold at which the segment
   stops predicting and must be retired or merged, plus a review date. Segments rot as the product
   changes, and an unreviewed segmentation outlives its truth by years.

## Output

- **Decision statement** — the one sentence from step 1.
- **Clustering basis** — variables used, why each, what was excluded and why.
- **Segment table** — name, defining behavior, accounts, share of revenue, revenue per account,
  retention, geography note.
- **Separation evidence** — variance check, the out-of-basis outcome each segment differs on,
  and the roadmap test result including any merges performed.
- **Per segment** — need in one sentence, top intervention, what would surprise you.
- **Kill criteria and review date** — per segment, the metric and threshold.
- **Not a segment** — cuts considered and rejected, each with the reason.

## Quality bar

- No demographic or firmographic variable appears in the clustering basis.
- Every segment differs on at least one outcome that was not used to build it.
- No two segments share a top intervention. Any that did were merged.
- Sizing reports revenue share, not only account counts, and states geography.
- There are five segments or fewer, each named by behavior.
- Every segment has a written kill criterion with a threshold and a date.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now on each segment claim. It is a mandatory gate: a segmentation is a
  finding, and it reaches nobody before it survives that interrogation.
- Invoke `sampling-plan` next unless the user redirects, so recruiting quotas use these cuts.
- If a narrative layer is genuinely needed for a decision, invoke `persona-builder` instead.
- If per-segment priorities are still unranked, invoke `maxdiff-and-tradeoff`.
- Invoke `opportunity-backlog` with the revenue sizing so segments compete against other work.
- Stop here once each segment has a kill criterion and a review date.
