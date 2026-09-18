---
name: research-repository-hygiene
description: >
  This skill should be used when the user asks to "organize our research repository", "nobody can
  find past research", "clean up our insights library", "set up a research repo", or needs a
  findable and trustworthy store of past findings. Produces a question-indexed entry schema,
  decision-area tags, expiry dates, and a review cadence.
metadata:
  version: "0.1.0"
  stage: "ops"
---

# Research Repository Hygiene

Organize by question answered, not by study name or date. Nobody searches for a study they did
not run, and a folder tree of study titles is a graveyard with good metadata. Every entry also
carries an expiry date, because a stale finding presented as current is worse than no repository
at all. It launders a guess into evidence.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Setting up or restructuring `~~research repository`.
- A finding was re-researched because nobody could find the original.
- Filing the output of a finished study so it is retrievable a year from now.
- Do not use this when the question is whether evidence already exists for a new request. Use
  `prior-evidence-check`, which searches what this skill maintains.

## Gather first

1. What already exists in `~~research repository`, roughly how many entries, and how they are
   currently organized.
2. Who searches it, how often, and the last three searches that failed.
3. The decision areas the organization actually runs, from the decisions section of the product
   context.
4. Who owns review and how much time per month they have.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Make the question the title of every entry.** Phrase it the way a stakeholder would type it,
   not the way the brief phrased it. One entry answers one question. A study that answered four
   questions becomes four entries, each linking back to the same study.
2. **Write each entry as an atomic insight with an evidence link.** One claim, the evidence
   behind it, and a link that resolves to the raw material in one click: the transcript passage,
   the query, the recording timestamp. A claim whose evidence is one more click away than the
   claim itself will be quoted without the evidence.
3. **Tag by decision area, not by feature or method.** Build the tag vocabulary directly from
   the decisions section of the product context and freeze it. Add a segment tag using the
   segment names in the product context, and record the geography and the population the sample
   was drawn from. A finding with no stated sample frame cannot be reused safely, because the
   next reader will apply it to a population it never described.
4. **Set an expiry date at filing time, based on how fast the subject changes.** Six months for
   anything about a surface under active development or about pricing. Twelve months for
   behavior and motivation findings. Twenty-four months for foundational segment and journey
   work. Entries past expiry render as expired, not as deleted, and cannot be cited until
   revalidated.
5. **State the confidence and the sample frame in the entry header, above the claim.** Method,
   n, population, dates, and known sampling limitations, including the named sampling trap from
   the product context where it applies. Burying this below the finding guarantees it is skipped.
6. **Run a monthly review of the next sixty days of expiries.** Each expiring entry gets one of
   three verdicts. **Revalidate** with a fresh date and the evidence used. **Archive** with a
   one-line reason, keeping it searchable and clearly marked. **Replace** with a link to the
   newer entry. Reviewing at expiry rather than in a yearly sweep keeps the batch small enough
   to actually happen.
7. **Handle a contradicted finding by superseding, never by deleting.** Mark the old entry
   Contradicted, link the contradicting entry, and write one line on what changed: the product,
   the population, or the method. Both stay searchable. Deleting the old one destroys the record
   of how the understanding changed, and the old claim keeps circulating in decks regardless.
8. **Enforce the entry schema at filing.** An entry missing sample frame, evidence link, or
   expiry is not filed. A partially filed entry is worse than an absent one, because it looks
   authoritative.

## Output

Entry schema:

- **Question** — stakeholder phrasing, in the title.
- **Answer** — one claim, three sentences maximum.
- **Confidence and sample frame** — method, n, population, geography, dates, limitations.
- **Evidence** — direct links to the raw material supporting the claim.
- **Tags** — decision area, segment, funnel stage.
- **Filed** / **Expires** / **Status** — current, expired, revalidated, archived, contradicted.
- **Source study** — link, plus other entries from the same study.
- **Supersedes / Superseded by** — links.

Also produce, when restructuring: the frozen decision-area tag list, the expiry policy by
subject type, the monthly review checklist, and the migration order for existing entries, oldest
high-traffic first.

## Quality bar

- Every entry title is a question in stakeholder language.
- Every entry makes exactly one claim and links to its own evidence.
- Every entry states sample frame and population above the claim.
- Every entry has an expiry date drawn from the stated policy.
- Tags come only from the frozen decision-area list built from the product context.
- Contradicted entries are marked and linked, never removed.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `insight-writer` now on any entry whose claim is not in the three-part form the schema
  requires.
- If two entries contradict each other, invoke `triangulation` instead of superseding on a guess.
- If entries expire within sixty days, invoke `opportunity-backlog` to carry the still-live ones
  forward.
- Invoke `impact-tracker` next unless the user redirects, so filed findings link to what they
  changed.
- If findability keeps failing across reviews, invoke `research-process-audit`.
- Stop here once the review is logged and the next review date is set.
