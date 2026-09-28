---
name: affinity-synthesis
description: >
  This skill should be used when the user asks to "do affinity mapping", "cluster the
  research notes", "find the themes across sessions", "build an affinity diagram", or needs
  to turn raw session observations into themes. Produces traceable notes, silent clusters,
  and sentence-form theme claims built bottom-up from evidence.
metadata:
  version: "0.1.0"
  stage: "synthesis"
---

# Affinity Synthesis

Themes are built upward from single observations, never sorted downward into buckets someone
drew before reading the data. Pre-made buckets guarantee the study confirms the brief. The
unit of work is one observation per note, written in the participant's words, carrying an ID
that points back to the source, because a note already translated into the team's language
has already been interpreted and the interpretation can no longer be audited.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- Fieldwork is done and 5 to 20 sessions of debriefs or transcripts need turning into themes.
- A qualitative corpus is small enough that every observation can be read individually.
- Do not use this when the corpus is too large to read note by note, typically over roughly
  400 notes or 25 sessions, use `thematic-coding`. Do not use it when the answer is needed
  today, use `lightning-synthesis`. Do not use it to write up a single theme once found, use
  `insight-writer`.

## Gather first

1. The corpus: which sessions, which segments from the product context, and whether debriefs
   or full transcripts are available.
2. The research question the synthesis must answer.
3. Who else is participating in the clustering, and whether they observed sessions.
4. Where the output will live in the `~~research repository`.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Make notes under four rules.** One observation per note. Written in the participant's
   language, preferring their exact phrasing. Carries an ID of the form `P07-14:32` that
   resolves to a participant and a moment. Understandable standing alone, with no reliance on
   the note beside it. Reject any note with a plural subject, it is a conclusion
   wearing a note's clothing. Expect 20 to 60 notes per hour of session.
2. **Strip the team's vocabulary.** Scan for notes using product or team terminology the participant
   never said and rewrite them from the source. Use the participant-facing language rules in
   the product context to decide which words are the product's framing rather than the user's.
3. **Cluster silently and without labels.** Move notes next to notes that feel like the same thing. No
   talking, no naming, no headers. Naming early collapses a cluster into its label and
   everything ambiguous gets filed under it. A single-note orphan is a legitimate result.
4. **Split anything over roughly twelve notes.** Large clusters are almost always two ideas
   sharing a topic. Force the split and see whether the halves say different things.
5. **Name each cluster with a full sentence that makes a claim.** The name must have a subject
   and a verb and be capable of being wrong. "Onboarding" is a topic and is banned. "People
   who arrive already skeptical treat the first result as a test of the tool, not of
   themselves" is a claim. If a cluster cannot be stated as a falsifiable sentence, it is not
   a theme yet, keep splitting.
6. **Count sources per cluster, never mentions.** Record how many distinct participants and
   how many distinct segments support each theme. A theme carried by one participant across
   nine notes is one source.
7. **Run the disconfirming pass.** Go back through the full corpus hunting specifically for
   the cluster nobody wanted to find: notes that contradict the strongest theme, notes that
   were quietly left as orphans, and any theme that exists only in one segment while the
   brief assumed it was universal. Write up what the disconfirming evidence would have to look
   like if the theme were false, then state whether it appeared. Skipping this pass is the
   single most common way an affinity wall becomes a mirror.
8. **Check the theme set against sampling.** Note which segments from the product context are
   absent from the corpus and mark any theme that cannot be generalized past the segment it
   came from.

## Output

```markdown
# Affinity synthesis — [study name]
Corpus: N sessions · [segments] · M notes · clustered [date]

## Themes
### T1. [Full-sentence claim]
Sources: N of M participants · segments: [...]
Evidence: [3-5 note IDs with verbatims]
Counter-evidence found: [...] | none found
Confidence: high | medium | low, and why

## Orphans kept
## Disconfirming pass
What would falsify the strongest theme, and whether it appeared.

## Coverage gaps
Segments absent from this corpus and what cannot be concluded as a result.
```

## Quality bar

- Every theme name is a sentence with a subject and a verb that could be proven wrong.
- Every note ID resolves to a participant and a moment in the source.
- Source counts are per participant and per segment, never mention counts.
- The disconfirming pass is written up, including when it found nothing.
- No theme uses product or team vocabulary that participants did not use.
- Clusters over twelve notes have been split or justified in one line.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `insight-writer` next unless the user redirects, once per surviving theme.
- Invoke `so-what-checker` now on those insights. Mandatory gate: no theme reaches a
  stakeholder before it is interrogated.
- If any theme contradicts behavioral data, invoke `triangulation` before reporting.
- If a surviving theme has a decision attached, invoke `pyramid-report` for the writeup.
- Stop here if no cluster survived the full-sentence claim test; there is no theme yet to
  report.
