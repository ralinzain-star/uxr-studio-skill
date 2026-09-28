---
name: card-sort-design
description: >
  This skill should be used when the user asks to "run a card sort", "test our information
  architecture", "figure out how users group our content", or needs to learn the mental model
  behind a navigation or content space. Produces a two-phase card sort plan with the card set,
  participant instructions, moderation notes, and the analysis rules for reading clusters.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Card Sort Design

Run an open sort first to learn the vocabulary, then a closed sort to validate a candidate
structure. Running only a closed sort tells you whether your structure is memorable, not whether
it is right, because the categories you supply are the answer you were trying to test.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- You are naming or grouping a content space and the grouping is contested internally.
- Users cannot find things and you do not yet know whether the problem is the grouping or the
  words. A card sort answers grouping. Build the example from the features-by-name list in the
  product context: ask whether several of those features are one idea to users or several.
- A feature portfolio has grown by accretion and the navigation reflects the org chart.
- Do not use this when a structure already exists and you need to know if people can find
  things in it. Use `tree-test-design`. Do not use it to name a single feature.

## Gather first

1. The content space and its boundary: what is in scope and what is deliberately excluded.
2. Whether a candidate structure already exists, and who authored it.
3. The audience segments whose model matters, since novices and experts sort differently.
4. Timeline, which decides moderated versus unmoderated.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Build the card set at 30 to 60 cards.** Below 30 the clusters are trivial. Above 60,
   fatigue collapses the sort into a few large piles and the data degrades quietly. If the
   space is larger, sample it: cover every branch and every content type rather than every item.
2. **Write each card in the user's language, not the product's.** Use the words from support
   tickets in `~~support desk`, search queries in `~~product analytics`, and review text in
   `~~review sites`. A card labelled with the internal feature name tests brand recall, not
   mental model. Take each feature named in the product context and write its card as the
   outcome the user is after, in their words. The feature's own name is a logo, not a card.
3. **Keep cards at one concept and roughly one length.** A card that bundles two ideas gets
   sorted on whichever half the participant read first. Long cards attract attention and warp
   the piles.
4. **Run the open sort with 15 to 20 participants per segment.** Participants make their own
   groups and name them. The names are the deliverable, not a byproduct. Require a name for
   every group and forbid "misc" without an explanation.
5. **Allow one "I do not know what this is" pile and one "does not belong" pile.** Cards that
   land there repeatedly are a comprehension finding worth more than the clusters.
6. **Choose moderated or unmoderated deliberately.** Moderated for the open sort, 8 to 10
   sessions, because the reasoning while sorting is the richest data and you can ask "what made
   these belong together" at the moment of placement. Unmoderated for the closed sort, where
   you want volume and the reasoning matters less. Do not run an unmoderated open sort as your
   only qualitative input.
7. **Build the candidate structure from the open sort, then run the closed sort** with 30 or
   more participants per segment against those fixed categories. Report per-card placement
   agreement, not an overall score.
8. **Read the dendrogram at a stated cut height and say what it is.** Clusters that only form
   above roughly 60 percent agreement are noise. Do not name a cluster that merges below that
   threshold, and never present a dendrogram without the cut line drawn.
9. **Read the agreement matrix for pairs, not for the diagonal.** Look for the two cards that
   cosort at 80 percent or more, which is a real grouping, and the card that cosorts with
   nothing above 40 percent, which is an orphan and usually a naming problem rather than a
   placement problem.
10. **Do not over-read weak clusters.** Any grouping under 50 percent agreement is a hypothesis
    for `tree-test-design`, not a finding. Say so in the report in those words.

## Output

- **Study question and decision** — one line each.
- **Card set** — numbered, with the source of each label and the item it stands for.
- **Open sort protocol** — instructions verbatim, group-naming rule, the two allowed extra
  piles, and the moderator probes.
- **Closed sort protocol** — the candidate categories, instructions verbatim, target n.
- **Analysis plan** — the dendrogram cut height, the agreement thresholds, and the orphan rule.
- **Reporting frame** — confirmed groupings, contested cards, orphans, vocabulary findings.
- **Assumptions** — anything inferred from the product context.

## Quality bar

- The card set is 30 to 60 cards, each one concept, each in user language.
- No card uses an internal feature name unless testing that name is the stated goal.
- The open sort precedes the closed sort, or the document states why it could not.
- Every reported cluster carries an agreement percentage and a stated cut height.
- Weak clusters are labelled hypotheses, not findings.
- Sample geography and segment are stated, per the product context sampling trap.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: the sort is not fielded with
  participants until it clears.
- Invoke `screener-builder` next unless the user redirects, then `participant-comms` to recruit
  per segment.
- Invoke `tree-test-design` next unless the user redirects, once the closed sort yields a
  candidate structure.
- If a grouping sits under 50 percent agreement, invoke `tree-test-design` on it as a
  hypothesis instead of `insight-to-spec`.
- If the sort produced vocabulary findings, invoke `insight-writer` on them.
- Stop here if only a closed sort is possible, and say what it cannot answer.
