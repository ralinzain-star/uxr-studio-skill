---
name: triangulation
description: >
  This skill should be used when the user asks "the survey and the interviews disagree",
  "analytics says the opposite of what users told us", "how do we reconcile these numbers",
  "which source should we believe", or needs to combine qualitative and quantitative evidence
  that conflict. Produces a reconciliation that names the cause of the disagreement.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Triangulation

When two methods disagree, the disagreement is the finding. Do not average it, do not pick the
source with the bigger sample, and do not quietly drop the qualitative side because it has an
n of twelve. Almost every apparent contradiction resolves the same way: the two sources
measured different populations, different timeframes, or different constructs. Check those
three before concluding that either source is wrong.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- Interview themes contradict `~~product analytics` or a `~~data warehouse` metric.
- Survey results and moderated sessions point opposite directions.
- `~~support desk` volume implies a problem that behavioral data says is rare, or the reverse.
- Do not use this when the sources agree and only need combining into one narrative, use
  `pyramid-report`. Do not use it to analyze a single quantitative source, use
  `survey-analysis` or `funnel-diagnostics`.

## Gather first

1. Both claims, stated precisely, with their exact measures and wordings.
2. For each source: population, sample size, date range, and how the measure was defined.
3. The decision that hangs on the resolution.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Restate both claims in one grammar.** Write each as subject, behavior, population,
   timeframe, magnitude. Most "contradictions" evaporate here because one claim turns out to
   have no population or no timeframe attached and was never comparable.
   **Hard stop: if either source is missing its population or its timeframe, do not continue.**
   Ask for them with AskUserQuestion and wait. Never assume both sources cover the same people or
   the same window, never write "TBD", and never proceed on the expectation that the base will be
   confirmed later. Reconciling two claims whose populations are unstated produces a mechanism
   that sounds right and is unfalsifiable. The only exception is an explicitly unattended run, in
   which case emit the verdict `Blocked: population or timeframe missing for <source>` and
   nothing else.
2. **Run the three checks, in this order, before judging either source.**
   - **Population.** Who is actually in each sample. Compare against the segments and the
     sampling trap in the product context. A metric computed over all traffic and a study
     recruited from paying users are not describing the same people, and the trap named in the
     product context will bias the broader one specifically.
   - **Timeframe.** What window each covers. Qualitative sessions describe recent memory,
     usually weeks. Metrics often cover quarters. Where the product context gives a
     feedback-loop length, check whether one source is measuring inside a loop the other has
     already completed. Episodic behavior looks like contradiction when the windows differ.
   - **Construct.** What each actually measures. Self-reported intent, self-reported past
     behavior, and logged behavior are three different constructs. So are "did not use it" and
     "did not find it". Two sources measuring adjacent constructs will disagree and both be
     correct.
3. **Only after all three checks, ask which source is more trustworthy for this question.**
   Trust logged behavior over self-report for what people did, how often, and in what order.
   Trust qualitative over behavioral for why, for what was attempted and abandoned, for what
   happened outside the product, and for anything involving intent or trust. Behavioral data
   cannot see a user who solved the problem elsewhere. Self-report cannot see frequency.
   Never let sample size alone decide, a large sample of the wrong construct is confidently
   wrong.
4. **Look for the reconciling mechanism.** State a single mechanism that would make both
   observations true at once. This is usually the real insight, and it is usually more
   specific than either source alone. Where two plausible mechanisms exist, name both and say
   which cheap test separates them.
5. **Write the finding to hold both.** Lead with the mechanism, then show both sources as
   evidence for it rather than as rivals. Never write "despite what users said".
6. **Call it unresolved when it is.** If the three checks find no difference in population,
   timeframe, or construct, and no single mechanism explains both, declare it unresolved, say
   what each source would license on its own, and specify the study that would settle it.
   Forcing a conclusion here is worse than leaving it open, because a forced reconciliation
   gets cited and the open question gets researched.

## Output

```markdown
# Triangulation — [question]

## The two claims
| | Source A | Source B |
| Claim | | |
| Population | | |
| Timeframe | | |
| Construct | | |
| n and date | | |

## Where they differ
Population / timeframe / construct, with the specific difference found.

## Reconciling mechanism
One mechanism making both true, or two candidates and the test between them.

## The finding
Written to hold both sources, mechanism first.

## Trust weighting
Which source leads for which part of the question, and why.

## Unresolved
What remains open, what each source licenses alone, and the study that would settle it.
```

## Quality bar

- Both claims are restated with population, timeframe, and construct made explicit.
- All three checks are documented, including the ones that found no difference.
- No conclusion rests on sample size alone.
- The finding names a mechanism rather than declaring a winner.
- Behavior leads for what happened, qualitative leads for why, and the split is stated.
- If unresolved, it says so plainly and names the study that would resolve it.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `insight-writer` next unless the user redirects, stating the mechanism as the claim.
- Invoke `so-what-checker` now on that claim. It is a mandatory gate: a reconciled finding gets
  quoted harder than either source was.
- Invoke `pyramid-report` once the claim survives.
- If the three checks found no difference and no mechanism holds both sources, invoke
  `mixed-methods-designer` to scope the settling study, then `research-roadmap` to place it.
- Stop here if the settling study cannot land before the decision date. Report it unresolved and
  say what each source licenses alone.
