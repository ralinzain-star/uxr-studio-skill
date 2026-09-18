---
name: workflow-ia
description: >
  This skill should be used when the user asks to "fix the navigation", "people can't find
  anything", "restructure the menu", "run a card sort", or needs to redesign and validate an
  information architecture. Produces the ordered chain from evidence check to validated
  structure and spec, with the gate at each step.
metadata:
  version: "0.1.0"
  stage: "workflow"
---

# Workflow: Information Architecture

An information architecture is fixed in two moves, never one: generate a structure from how users
group things, then prove people can find things in it. A card sort alone produces a menu nobody has
navigated. A tree test alone proves the current structure fails without saying what replaces it.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Users cannot find features that exist, or support traffic is dominated by "where is".
- A surface has grown by accretion, or a restructure is drafted and needs validating.
- Do not use this when the problem is that a feature is unconvincing rather than unfindable.
  That is `workflow-discovery` or `workflow-usability`.

## Gather first

1. Which surface, and the full inventory of items to be organized.
2. Who navigates it, and whether segments in the product context look for different things.
3. Whether labels can change. A fix that cannot rename anything is half a fix, and saying so early
   prevents a wasted round.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## The chain

Realistic elapsed time: five to six weeks. Two unmoderated studies with recruiting between them is
the long pole. One recruited pool can serve both if it is large enough to keep the samples
independent.

1. `prior-evidence-check` — consumes the complaint. Produces what is known: search terms, dead-end
   paths and "where is" tickets from `~~support desk` and `~~product analytics`.
   **Abort gate:** if the data shows people find the item and abandon after arriving, this is not
   an IA problem. Stop and route to `workflow-usability`. Findability and persuasion look
   identical in a complaint and need opposite fixes.
2. `card-sort-design` — consumes the inventory. Produces the sort: open for a new structure,
   closed to test a drafted one, hybrid when some top level is fixed. Gate: the item set is the
   real inventory in user-facing words, not internal feature names. Use the feature names in the
   product context only to build the inventory, never as card labels.
3. `affinity-synthesis` — consumes sort results. Produces candidate groupings with agreement
   scores and the items no group claimed. Gate: orphans named, not forced into a category to tidy
   the diagram, and segment disagreements reported rather than averaged into one structure.
4. `tree-test-design` — consumes the candidate structure. Produces findability tasks against the
   proposed tree and, where possible, the current one as a baseline. **Recruiting for this step
   runs in parallel with step 3.** Steps 2 and 4 themselves cannot overlap: the tree does not
   exist until the sort is synthesized. Gate: tasks phrased as what the user wants, never
   containing a label from the tree.
5. `quant-usability-metrics` — consumes tree test results. Produces success rate, directness and
   first-click accuracy per task and structure, with intervals. Gate: the new tree beats the
   baseline on the tasks that matter, not on average. **Abort gate:** if it does not, do not ship
   it. Loop back to step 3 with the failing tasks.
6. `insight-writer` — consumes the metrics and the sort. Produces insights naming which items move
   where and why, with the evidence for each. Gate: each names an item and a destination.
7. `insight-to-spec` — produces the structure as a spec: final tree, label changes, redirects, and
   the tasks to re-measure after launch. Gate: owned in `~~project tracker`, with the post-launch
   measure written before the change ships.

## Checkpoints

- After step 1: this is a findability problem, confirmed.
- After step 3: segment differences surfaced, orphan items listed.
- After step 5: the new tree beats the baseline, or the chain loops.
- After step 7: a post-launch re-measure is scheduled.

## Quality bar

- The card set uses user language, and tree test tasks never quote a label.
- The two studies use independent samples.
- Every proposed move traces to a sort result, a tree test result, or both.
- A baseline comparison exists, or its absence is stated as a limitation, and the spec includes
  the measure that will confirm the fix worked.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `tree-test-design` again on the same tasks after launch, and log the result in
  `impact-tracker`.
- Invoke `research-repository-hygiene` to file both studies.
- Invoke `prior-evidence-check` now to begin: it is step 1 of the chain above. Work the steps in
  order in this same turn, without pausing for approval between them, stopping only at a
  declared abort gate.
