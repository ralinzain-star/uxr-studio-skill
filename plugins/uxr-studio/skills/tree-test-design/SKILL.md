---
name: tree-test-design
description: >
  This skill should be used when the user asks to "run a tree test", "test findability", "check
  if people can find things in our nav", or needs to know whether a proposed structure works
  before anything is designed. Produces a tree test plan: the tree, findability tasks with
  correct answers, the metric thresholds, and the rules for diagnosing each failure pattern.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Tree Test Design

Tree testing is the only cheap way to separate a navigation problem from a labelling problem,
and it has to run before visual design, not after. Once there is a comp, every failure gets
blamed on the layout and the structure ships unexamined.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A proposed information architecture exists on paper and nobody has tested it on a stranger.
- Two candidate structures are in dispute and you want a number rather than an argument. Run
  both trees between subjects with the same tasks.
- Analytics show people landing in the wrong place. Anchor the example in one of the standing
  problem areas from the product context, typically a named feature with an adoption cliff that
  users reach for and do not find.
- Do not use this when you do not yet know how users group the space. Use `card-sort-design`
  first. Do not use it to test a rendered interface. Use `usability-test-plan`.

## Gather first

1. The tree: every node, every level, exactly as proposed, including labels you dislike.
2. The tasks that matter, expressed as goals users actually have.
3. Whether one tree or two are being compared, and the decision date.
4. The target segment, since findability differs sharply between new and returning users.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Write each task as a goal, never using the label being tested.** If the task sentence
   contains the target node's word, you have tested reading, not findability. Build each task
   this way: take the destination feature by name from the product context, write down the
   outcome a user wants from it in their own words, then delete every word of the label and every
   synonym of it from the sentence. Test the result by asking whether someone could reconstruct
   the label from the task text. If they could, rewrite it. This is the rule the whole method
   rests on, and it is the one most often broken.
2. **Write tasks in the participant's situation, not the product's.** Lead with the trigger
   state. Keep each task under 30 words and free of jargon the participant would not use.
3. **Define the correct answer, and the acceptable alternates, before fielding.** Some tasks
   have two defensible destinations. Decide which count as success now, not after seeing the
   data.
4. **Cap at 8 to 12 tasks.** Fatigue starts around task 10 and directness collapses first.
   Randomize task order per participant and never put the most important task last.
5. **Recruit 50 or more per tree.** Tree testing is quantitative. Under 30, the confidence
   interval on success rate is wider than the difference you are trying to detect.
6. **Track four metrics and read them in this order.** Success: did they end on a correct node.
   Directness: did they get there without backtracking, which is the real measure of confidence
   in the structure. First click: which top-level node they chose, which localizes the failure
   better than anything else. Time: last, and only as a tiebreaker. A high success rate with low
   directness means people found it by exhaustive search, which will not survive a real
   interface.
7. **Diagnose each failed task by its first-click pattern, and let the pattern pick the fix.**
   If first clicks scatter across three or more branches with no majority, the label is
   meaningless and the node needs renaming. If first clicks concentrate on one wrong branch,
   users have a coherent model that disagrees with yours and the node needs moving to where they
   looked. If first clicks are correct but success is low, the failure is deeper in the tree, so
   fix the child labels, not the parent. If success is high but directness is low, the label is
   right and the sibling labels are too close together.
8. **Set thresholds before you look.** Treat below 60 percent success as a failure requiring a
   change, 60 to 80 as a watch item, above 80 as a pass. State these in the plan so nobody
   negotiates them afterwards.
9. **Retest the changed nodes on a fresh sample.** A tree test is cheap enough that shipping an
   untested revision is a choice, not a constraint.

## Output

- **Study question and decision** — one line each, with the decision date.
- **Tree** — the full structure as an indented list, with every node labelled.
- **Task table** — task number, task text verbatim, correct node, acceptable alternates, and
  the node the task is really probing.
- **Fielding plan** — n per tree, segment, geography, randomization, `~~survey tool` or panel
  source, task order rules.
- **Metric thresholds** — success, directness, first click, time, with the pass bands stated.
- **Diagnosis key** — the four first-click patterns and the fix each one implies.
- **Reporting frame** — per-task result, failure diagnosis, the specific rename or move
  recommended for each failing node.
- **Assumptions** — anything inferred from the product context.

## Quality bar

- No task sentence contains a word from the node it is testing.
- Every task has a written correct answer and its acceptable alternates, set before fielding.
- Task count is 12 or fewer and order is randomized.
- Sample is 50 or more per tree, with geography stated per the product context sampling trap.
- Success and directness thresholds are written down before any data is seen.
- Every recommendation names either a rename or a move, justified by a first-click pattern.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now, before the test is fielded. It is a mandatory gate: an
  unmoderated study still collects data from real people.
- Invoke `sample-size-advisor` next unless the user redirects, to confirm 50 or more per tree
  against the segment and the geography trap.
- If a task lands below the 60 percent success threshold and first clicks scatter, invoke
  `card-sort-design` instead of renaming by guess.
- If the tree passes, invoke `insight-to-spec` with the renames and moves.
- Invoke `topline-writer` when the decision is being made in a review.
