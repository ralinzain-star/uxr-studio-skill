---
name: so-what-checker
description: >
  This skill should be used when the user asks to "sanity check these findings", "is this
  insight any good", "review the report before it goes out", or has a finding that reads true
  but changes nothing. Produces a per-claim verdict of keep, rewrite, or kill against a fixed
  five-question interrogation.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# So What Checker

Run this on every deliverable, not just the ones that feel weak. The findings that waste the most
organisational attention are the ones that read as competent and demand nothing, and those are
precisely the ones nobody flags. A claim that survives all five questions with no answer does not
get a caveat. It gets cut.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- Any finding, insight, topline, report, or shareout deck is about to ship.
- A stakeholder nodded at a readout and nothing happened afterwards.
- A findings list has grown past a handful of items and needs culling.
- Do not use this when the claims are not yet written and you need to draft them. Go to
  `insight-writer` first, then bring the output back here.

## Gather first

1. The claims themselves, each as a standalone sentence.
2. The decision each claim is meant to inform, from the decisions section of the product context.
3. The evidence behind each claim: method, n, segment, and strength.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Split the deliverable into atomic claims.** One sentence, one assertion. A paragraph that
   contains three assertions gets tested as three claims, because a weak claim hides inside a
   strong one.
2. **Interrogate each claim with these five questions, in this order, and write the answer down.**
   Do not skip a question because the answer seems obvious.
   - **So what?** What is worse or more expensive because this is true?
   - **Who would act on it?** Name a role. Not a team, not "the business".
   - **What would they do differently?** Name the concrete change. "Consider" and "be aware of"
     are not changes.
   - **What would it cost them to be wrong?** If acting on the claim and being wrong costs
     nothing, the claim is decoration.
   - **Could this have come out the other way?** If no realistic session could have produced the
     opposite result, you measured your own framing.
3. **Score and rule.** Five answers means keep. One or two blanks means rewrite, and say which
   question the rewrite must answer. Three or more blanks means kill, and say so plainly rather
   than downgrading it to a "secondary finding".
4. **Detect the four failure shapes explicitly, one pass each.**
   - **Restated observation.** The claim describes what happened in sessions without asserting
     anything beyond them. Test: strip the hedges and see whether a sentence remains. Fix by
     naming the mechanism or the consequence, or cut it.
   - **Unfalsifiable claim.** Usually contains "users want", "there is friction", or "expectations
     are not met". No result could contradict it. Fix by attaching a condition and a threshold.
   - **Recommendation nobody owns.** Passive voice, or an owner named as a group. Fix by naming
     the role that controls the change, or move it to `opportunity-backlog` until one exists.
   - **Confirms the existing plan.** The finding agrees with what the team already intended to
     build, so nothing changed. This is not automatically bad. Mark it as confirmatory, state
     what it de-risked and what it would have cost to be wrong, and never let it occupy a top
     slot in a report. If it de-risked nothing, cut it.
5. **Check the claim against the standing problem areas.** A claim that advances none of the
   known problem areas and feeds none of the listed decisions in the product context needs an
   explicit reason to survive.
6. **Check the sample can carry the claim.** A segment-level assertion drawn from a sample that
   hit the named sampling trap gets rewritten to the population actually sampled, or killed.
7. **Do not soften a failing claim into a caveat.** "Directionally, users may..." is a kill
   dressed as a keep.

## Output

A table, one row per claim:

```
Claim | So what | Who acts (role) | What changes | Cost of being wrong | Falsifiable?
      | Failure shape (if any) | Verdict: keep / rewrite / kill | Rewrite instruction
```

Then: the rewritten keepers in final wording, the killed claims listed with one-line reasons so
the decision is visible, and a count of claims in, kept, rewritten, killed.

## Quality bar

- Every claim was tested against all five questions with a written answer or an explicit blank.
- Each of the four failure shapes was checked for by name across the set.
- Every kill is listed with its reason rather than quietly dropped.
- No surviving claim uses "users want", "friction", or an unowned recommendation.
- Confirmatory findings are labelled as such and state what they de-risked.
- The rewritten claims are shown in final wording, ready to paste back.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects. This
skill is the mandatory gate before anything ships, so its own per-claim verdicts decide the route.

- If any claim survives, invoke `pyramid-report` next unless the user redirects. Invoke
  `topline-writer` instead when the readout is due within a day.
- If a surviving keep names an owner, invoke `insight-to-spec` for it.
- If a surviving keep has no owner, invoke `opportunity-backlog` for it.
- Invoke `impact-tracker` once the readout produces a logged decision.
- Stop here if nothing survives. Say so plainly, name what was examined and what was not found,
  and never ship a weaker report assembled from killed claims.
