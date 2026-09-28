---
name: democratization-guardrails
description: >
  This skill should be used when the user asks to "let PMs run their own research", "set up
  research democratization", "should designers run usability tests", "guardrails for
  self-service research", or needs rules for non-researchers doing research. Produces a tiered
  permission model, the templates and training each tier requires, and a publication checkpoint.
metadata:
  version: "0.1.0"
  stage: "ops"
---

# Democratization Guardrails

Democratize the methods where a mistake is recoverable and gate the ones where it is not. An
imperfect usability test wastes an hour. A badly sampled survey produces a number that gets
quoted in planning for two years and cannot be recalled. Usability tests and concept feedback
go out with a template. Sampling, survey instrument design, and anything quoted as a number
stay with a researcher.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- Demand exceeds researcher capacity and people are already going around the queue.
- A team wants standing permission to run its own sessions.
- A non-researcher has produced a finding about to be presented as fact.
- Do not use this when the problem is one badly run study rather than the permission model. Use
  `study-retro`.

## Gather first

1. Who wants to run research, how often, and what they have already run unsupervised.
2. Researcher review capacity in hours per week. It sets the ceiling on safe delegation.
3. What tooling non-researchers can reach: `~~survey tool`, `~~recruiting panel`,
   `~~scheduling tool`, `~~transcription tool`.
4. Any number circulating in planning that came from an unsupervised study.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Sort every method into three tiers by whether a mistake is recoverable.** Publish the tier
   list itself, so the answer to "can I run this" is a lookup rather than a negotiation.
   - **Tier 1, open with a template.** Usability tests on existing flows, concept feedback,
     observation and note-taking, and joining sessions someone else moderates. A bad session
     here produces weak input that the next session corrects.
   - **Tier 2, draft delegated, researcher reviews before fielding.** Interview and discussion
     guides, diary prompts, follow-up questions added to an existing study. The reviewer checks
     for leading questions and for the participant-facing language rules in the product context.
     Review is capped at 30 minutes, or Tier 2 becomes a queue and people route around it.
   - **Tier 3, researcher only.** Sampling and recruitment frames, screener logic, survey
     instrument design, anything producing a quotable number, pricing and tradeoff work,
     segmentation, and any study touching a sensitive disclosure or a vulnerable population as
     described in the product context. These fail silently and irreversibly.
2. **Gate on the output, not just the method.** Any study whose result will be stated as a
   percentage is Tier 3 however it was collected. Sample frame decides whether a number means
   anything, and it is what non-researchers get wrong first.
3. **Ship a template per Tier 1 method, not a training deck.** Each carries the task wording, a
   do-not-say list drawn from the participant-facing language rules in the product context, the
   consent and incentive text from `incentives-and-consent`, a note format `session-debrief` can
   consume, and a stop rule for ending a session and escalating.
4. **Run a 90-minute certification once, then one observed session.** Cover moderating without
   leading, say-versus-do, when to stop, and the named sampling trap in the product context as
   the worked example of a plausible sample producing a wrong answer. Expires annually.
5. **Put one checkpoint before anything becomes a published finding.** Nothing from Tier 1 or
   Tier 2 is written up as a finding, filed in `~~research repository`, or shown in a planning
   deck without a researcher signing the claim. Review the claim and the sample frame, not the
   session quality. It is a 15-minute check and the single control that makes the model safe.
6. **Design against the dominant failure mode: the internal survey that becomes organizational
   truth.** Someone posts a survey to a convenience sample, gets a clean percentage, and that
   percentage outlives everyone who knew how it was collected. Countermeasures: `~~survey tool`
   access is Tier 3, every reported number carries its sample frame in the same sentence, and
   numbers without a frame are struck from decks rather than caveated.
7. **Review the tier list quarterly against what went wrong.** A list that never loosens gets
   ignored. One that only loosens stops protecting anything.

## Output

- **Tier table** — method, tier, who may run it, review required, turnaround.
- **Output gate rule** — the rule promoting any quoted number to Tier 3.
- **Templates** — one per Tier 1 method, with links and owner.
- **Certification** — curriculum, duration, observed-session requirement, expiry.
- **Publication checkpoint** — what the reviewer checks, the time cap, who signs.
- **Escalation** — when a self-serve study must stop and come to a researcher.
- **Review** — the quarterly date the tier list is revisited.

## Quality bar

- Every tier assignment is justified by recoverability of the mistake, not by difficulty.
- Sampling, survey instrument design, and any quoted number sit in Tier 3 without exception.
- Every Tier 1 method has a template before that tier opens.
- The publication checkpoint is mandatory, capped in time, and names who can sign.
- Review load is within the stated researcher capacity. If it is not, shrink Tier 2.
- The sample-frame-with-every-number rule is enforceable, not advisory.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now on every delegated design, regardless of tier. Mandatory
  gate: self-serve studies are fielded by people who cannot spot a harm.
- Invoke `usability-test-plan` and `session-debrief` next unless the user redirects, to build
  the Tier 1 templates.
- Invoke `interview-guide-builder` next unless the user redirects, for the Tier 2 template.
- Invoke `incentives-and-consent` next unless the user redirects, for the consent and payment
  text every template carries.
- If an unsupervised number is already circulating, invoke `so-what-checker` on it now.
- Stop here once every tier has its template and the checkpoint has a named signer.
