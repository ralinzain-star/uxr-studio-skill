---
name: thematic-coding
description: >
  This skill should be used when the user asks to "code the transcripts", "build a codebook",
  "do thematic analysis", "analyze open-ended survey responses", "tag the support tickets",
  or needs systematic analysis of a corpus too large to cluster by hand. Produces an
  inductive frozen codebook, a reliability check, and source-count prevalence reporting.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Thematic Coding

Build the codebook inductively from the data, then freeze it and apply it consistently. A
codebook that keeps growing through the corpus means early and late material were analyzed
with different instruments and prevalence counts across them mean nothing. Report how many
distinct sources support a theme, never how many mentions, because one talkative participant
or one prolific reviewer can manufacture a theme out of a single opinion.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- The corpus is large: 25+ sessions, hundreds of open-ended survey responses, a ticket export
  from the `~~support desk`, or a scrape from `~~review sites`.
- Prevalence matters, not just existence: the question is how common something is, not only
  whether it happens.
- The same corpus will be re-coded later and the two runs must be comparable.
- Do not use this on a handful of rich sessions where every observation can be read
  individually, use `affinity-synthesis`. Do not use it under deadline pressure, use
  `lightning-synthesis`.

## Gather first

1. The corpus, its size, its source, and its date range.
2. What the unit of analysis is: a participant, a response, a ticket, a review.
3. The research question and whether prevalence or discovery is the goal.
4. Whether a second coder is available.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Sample the development set.** Take the first 20 to 25 percent of the corpus, stratified
   across the segments named in the product context so the codebook is not built on one
   population. Note the geography or sampling trap described in the product context and check
   the development set is not disproportionately drawn from it.
2. **Open-code the development set.** Assign short descriptive codes to every meaningful
   segment of text. Code inductively, from the text upward, and let the count run high. Fifty
   to ninety codes at this stage is normal and healthy. Do not deduplicate while coding.
3. **Consolidate into a codebook.** Merge synonyms, collapse codes appearing once, and split
   any code applied to visibly different things. Target 15 to 30 final codes. Every code gets
   four fields: name, one-sentence definition, an inclusion rule, and an exclusion rule naming
   the nearest code it is most often confused with. A code without an exclusion rule will
   swallow its neighbors.
4. **Freeze the codebook.** Version it and record the freeze date. From this point new
   material that fits nothing gets tagged `unassigned` rather than spawning a code. If
   `unassigned` exceeds 10 percent of the remaining corpus, stop, unfreeze, revise, and re-code
   the already-coded material from the start under version 2. Never run two versions across
   one corpus.
5. **Apply to the full corpus, including the development set again.** Re-code the development
   set under the frozen book so the whole corpus is coded once with one instrument.
6. **Run the reliability check.** Preferred: a second coder independently codes a random 15
   percent, then compare and report percentage agreement per code. Below 80 percent agreement
   on a code means the definition is broken, not that the coder is, so rewrite the definition
   and re-code that code across the corpus. Where no second coder exists, re-code a random 15
   percent yourself at least 24 hours later and report the intra-coder agreement, labelled
   honestly as the weaker check.
7. **Group codes into themes and report prevalence by source.** For each theme report: number
   of distinct sources, denominator, and breakdown by segment. Write "14 of 62 participants
   (23%)", never "47 mentions". Where one source contributes many coded segments, say so.
8. **Report the negative cases.** Name codes expected from the standing problem areas in the
   product context that did not appear, and say what their absence does and does not mean.

## Output

```markdown
# Thematic coding — [corpus] — codebook v[N], frozen [date]
Corpus: N units · source · date range · segment breakdown

## Codebook
| Code | Definition | Include | Exclude (vs. nearest code) |

## Themes and prevalence
### [Theme, stated as a claim]
Sources: N of M (X%) · by segment: [...] · codes rolled up: [...]
Representative verbatims with source IDs.

## Reliability
Method (second coder | intra-coder), sample size, per-code agreement, codes rewritten.

## Unassigned
Share of corpus, and what the residue looks like.

## Absent codes
Expected themes that did not appear, and the correct reading of that.
```

## Quality bar

- Every code has a definition, an inclusion rule, and an exclusion rule naming a rival code.
- The codebook has one frozen version applied to the entire corpus, including the development
  set.
- Prevalence is reported as sources over a stated denominator, with segment breakdown.
- A reliability check is reported with its method named and its weakness stated.
- Unassigned share is reported, and exceeded 10 percent only if the book was revised and the
  corpus re-coded.
- No theme rests on a single source, or it is explicitly labelled as a single-source signal.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `insight-writer` next unless the user redirects, turning each theme into a claim with
  its consequence.
- Invoke `so-what-checker` now on those claims. It is a mandatory gate: no theme is shown to
  anyone before it has run.
- If coded prevalence disagrees with `~~product analytics`, invoke `triangulation` before
  reporting either.
- Invoke `pyramid-report` once claims survive.
- Stop here if the unassigned share stayed above 10 percent after a re-code. The corpus needs a
  new book, not a report.
