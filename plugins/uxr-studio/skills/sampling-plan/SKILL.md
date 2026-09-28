---
name: sampling-plan
description: >
  This skill should be used when the user asks "who should we recruit", "where do we find
  these people", "is this sample representative", or needs to choose a sample frame and set
  quotas before fielding a study. Produces a named sample frame, an explicit exclusion
  statement, quota cells, and the claims the sample can and cannot support.
metadata:
  version: "0.1.0"
  stage: "recruiting"
---

# Sampling Plan

The sample frame is the biggest silent threat to any study. Where you recruit from determines
what you are allowed to conclude, and a frame chosen by convenience will quietly invalidate a
perfectly executed study. Name the frame, name who it structurally excludes, and quota only on
segments that could plausibly answer differently.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A study is scoped and you need to decide which population to draw from and in what mix.
- Someone proposes "just email our users" or "run it on the site" and nobody has said who that
  reaches.
- A finished study is being challenged on representativeness and you need to state its limits.
- Do not use this when the question is how many participants are enough for a given effect or
  confidence level — use `sample-size-advisor` first, then set the mix here.

## Gather first

1. The decision the study feeds, and which population that decision applies to.
2. Candidate sources available: `~~product analytics` cohorts, `~~data warehouse` lists,
   in-product intercept, `~~support desk` contacts, `~~recruiting panel`, `~~survey tool`.
3. Whether the question is about money (trial, pricing, renewal, churn) or about usability.
4. Any segment differences already suspected or observed.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write the target population in one sentence** — the group the decision will be applied to,
   not the group you can reach. "Users who start a trial and do not convert within 14 days,"
   not "people who answer our survey."
2. **Name the frame explicitly.** The frame is the concrete, addressable list you will actually
   draw from: a warehouse query, a panel's audience definition, an intercept placement. Write it
   as something someone else could re-run. "All accounts with a trial start in the last 30 days
   and no conversion event, from `~~data warehouse`" is a frame. "Our users" is not.
   **Hard stop: if the real frame is not known — the actual query, list or placement someone
   could re-run, and therefore who it structurally excludes — do not continue the plan.** Ask for
   it with AskUserQuestion and wait. Never infer a frame, never write "TBD", and never proceed
   assuming the source will be settled later. Quotas built on a guessed frame are a precise plan
   for the wrong population. The only exception is an explicitly unattended run, in which case
   emit the verdict `Blocked: no named sample frame` and nothing else.
3. **State who the frame structurally excludes.** List them. Anyone who churned before the
   window, anyone who never registered, anyone who unsubscribed from email, anyone whose
   behavior made them invisible to the query. This list is the study's limitations section and
   it must be written before fielding, not after a reviewer asks.
4. **Run the frame-invalidation test, and write the trap out in the plan.** A frame-invalidating
   trap is one where the easiest population to reach differs systematically from the population
   the decision applies to, on the exact dimension the decision turns on. It does not look like a
   mistake: the sample is large, the fielding is clean, the analysis is correct, and the
   recommendation describes people who were never in scope. A bigger n makes it worse, because
   size is what stops anyone questioning it.
   Use the sampling trap named in the product context as the worked example and state it in four
   lines: the convenient frame someone would reach for, the population it over-represents, the
   dimension on which that population differs from the decision population, and the specific
   claim that would therefore be wrong. Then give the corrected frame, drawn from the population
   the decision applies to, and record the sample's composition on the trap's dimension so a
   reader can check it. If no reachable frame escapes the trap, say the study cannot answer the
   question as asked rather than fielding a clean sample of the wrong people.
5. **Quota on segments that could plausibly differ, not on demographics that probably do not.**
   Write one line per proposed quota: "I expect X and Y to answer differently because Z." If you
   cannot write that sentence, drop the quota. Age, gender and company size are usually not
   the axis of variation; behavior, tenure, and funnel position usually are. Draw the candidate
   cells from the segment table in the product context, and add a cell wherever a single funnel
   stage or a single number in the known problem areas hides two groups with different causal
   stories. Then keep only the cells that pass the "expect to differ because" test and drop the
   rest, including any cell that is really a self-described role.
6. **Size the cells for comparison, not for the total.** If you intend to compare two cells,
   each needs enough on its own. For qual, 5–6 per cell and no more than three cells; beyond
   that you are running three studies with one budget.
7. **Add a contamination screen.** Exclude anyone who participated in a study in the last 90
   days, anyone employed by a competitor, and internal staff. Log the exclusion in the frame.
8. **Write the claims sentence.** Two lines: what this sample supports, and what it does not.
   The supports line names the population, its funnel position and its geography. The does-not
   line names at least the group the frame structurally excluded and the dimension the sampling
   trap concerns, in the shape "does not support claims about <excluded group>, or about
   <population the trap distorts>".

## Output

- **Target population** — one sentence.
- **Sample frame** — the re-runnable definition, plus the source system.
- **Structurally excluded** — bulleted list, each with why.
- **Quota table** — cell, target n, rationale sentence ("expect to differ because...").
- **Recruiting source and expected yield** — screener pass rate, no-show allowance, over-recruit
  number.
- **Geography statement** — one line, always present.
- **Claims this sample supports / does not support** — two short lists.
- **Assumptions** — what was inferred from the product context.

## Quality bar

- The frame is specific enough that a second person could reproduce the list.
- Every quota cell has a written "expect to differ because" sentence.
- The structurally-excluded list is non-empty. Every frame excludes someone.
- Geography is stated explicitly, and the frame has been tested against the sampling trap named
  in the product context, with the result written into the plan.
- The claims-not-supported list names at least one real limitation, not a formality.
- Over-recruit accounts for screener failure and no-shows with a stated percentage.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `screener-builder` next unless the user redirects, handing it the quota table and the
  criteria to disguise.
- If no reachable frame escapes the sampling trap, stop and return to `research-brief-builder`:
  the study cannot answer the question as asked.
- If the per-cell counts are not yet set, invoke `sample-size-advisor` first.
- Invoke `research-ethics-review` before fielding. It is a mandatory gate: the frame and its
  exclusions are part of what gets reviewed.
- If findings are later written from this sample, invoke `insight-writer` and `pyramid-report`
  carrying the claims-not-supported list verbatim.
