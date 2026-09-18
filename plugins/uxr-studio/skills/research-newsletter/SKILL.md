---
name: research-newsletter
description: >
  This skill should be used when the user asks to "send the research digest", "write the monthly
  research update", "keep the org aware of what research is finding", or needs a recurring
  publication that creates demand for research. Produces a fixed-format digest led by a finding
  that changes something, with a read-and-act measurement plan.
metadata:
  version: "0.1.0"
  stage: "reporting"
---

# Research Newsletter

The newsletter exists to create pull for research, not to archive it. Lead with a finding that
changes something and cut everything that reads like a status update. Nobody has ever requested a
study because they read that a study is in progress. The archive is the repository's job. This is
marketing for the evidence.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Research output is invisible outside the teams that commissioned it.
- Multiple studies have shipped and their findings are not reaching adjacent teams.
- A recurring slot exists or should exist to keep evidence in the organisation's view.
- Do not use this when a specific group must decide something. Run `research-shareout` instead.
  A newsletter is not a decision venue.

## Gather first

1. Findings shipped since the last edition, and which ones changed a decision.
2. Outcomes of previously published recommendations, from `impact-tracker`.
3. The audience list and the channel, and whether it is opt-in.

Ask only for what is missing, at most 2 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Pick a cadence you can hold for a year and publish on a fixed day.** Monthly is the default.
   Biweekly only where studies close that fast. Quarterly is an archive and will not create pull.
   A missed edition costs more readers than a thin one, so ship thin rather than late.
2. **Lead with one finding that changes something.** One, not a roundup. It must name what is now
   known, what somebody is doing differently because of it, and who. If no finding this period
   changed anything, lead with the most consequential open question instead and say plainly that
   nothing shipped changed a decision. That admission builds more credibility than filler.
3. **Write the headline as a claim, not a topic.** "What we learned about onboarding" is a topic
   and dies in the inbox. A claim that a reader can disagree with survives. Keep it under 10 words,
   put the surprising half first, use no colon-subtitle construction, and never use the study name
   as the headline. Test it by asking whether a reader who reads only the headline knows something
   they did not before.
4. **Hold the format fixed so readers learn where to look.** Same sections, same order, every
   edition. Variation costs recognition and buys nothing.
5. **Cap the length hard at roughly 600 words, or one scroll on a phone.** Every item links out
   to the full report in `~~research repository`. The digest is an index with opinions, not a
   summary of summaries.
6. **Cut anything that reads like a status update.** Studies in field, sample sizes recruited,
   tooling changes, team news, and roadmaps of upcoming research all go. The test: if an item
   tells a reader what the research team has been busy with rather than what they should now
   believe or do, delete it.
7. **Include one item that contradicts something the organisation believes.** If the edition has
   none, look harder before concluding there is none.
8. **Close with a single ask, always the same shape.** One open question, one way to bring a
   question in, one link to the top of `opportunity-backlog`.
9. **Measure acting, not opening.** Open rate measures your subject line. Track instead: intake
   requests attributable to an edition, clickthroughs to the linked reports, backlog entries
   referenced in other teams' planning documents, and decisions cited in `impact-tracker` that
   trace to a published finding. Review these every third edition and change the format when they
   fall, not when the content feels stale.
10. **Respect the language rules.** Anything quoting participants follows the participant-facing
    language and tone rules in the product context, including in a forwarded internal digest.

## Output

```
Subject           the headline claim, under 10 words
The finding       one finding, what changed, who is acting, link to report
Also worth knowing  2-3 one-line findings, each with a link and a consequence
This contradicts what we thought   one item, stated plainly
What shipped because of research   1-2 lines, outcomes not activities, from impact-tracker
The ask           one open question, how to bring a question in, link to the backlog
```

Send in `~~chat` and mirror it in `~~research repository` with its metrics appended after two weeks.

## Quality bar

- The lead is a finding that changed something, or an explicit statement that none did.
- The headline is a claim under 10 words with no colon-subtitle and no study name.
- Total length is at or under roughly 600 words.
- No item describes research activity, in-field status, or team process.
- Every item links out rather than reproducing the report.
- The measurement plan tracks actions taken, not opens.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `so-what-checker` now on the lead finding and every item in the edition. It is a
  mandatory gate: nothing reaches readers before it survives that interrogation.
- If an item has no recorded outcome yet, invoke `impact-tracker` to pull or open the entry
  rather than publishing a status line.
- Invoke `opportunity-backlog` next unless the user redirects, to refresh the standing list the
  edition links to.
- If a reader question arrives from the edition, invoke `research-intake-triage` on it.
- Stop here once the edition is sent and the read-and-act measures are scheduled.
