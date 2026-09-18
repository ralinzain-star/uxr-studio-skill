---
name: churn-interview
description: >
  This skill should be used when the user asks to "interview churned users", "why are people
  cancelling", "talk to people who downgraded", "win-back research", or needs to understand
  cancellation, downgrade, or dormancy. Produces a churn interview design that sorts
  outcome-churn from failure-churn before any cause is assigned.
metadata:
  version: "0.1.0"
  stage: "qual"
---

# Churn Interview

The reason a user picks on a cancellation form is a category, not a cause. Nobody cancelled
because of a radio button. In episodic products a large share of churn is a user succeeding, so
the first job of every churn study is sorting outcome-churn from failure-churn. A blended cohort
averages two unrelated populations and misleads everyone who reads it.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- Cancellations, downgrades, or dormancy are up and form reasons are not explaining it.
- Win-back or renewal messaging needs grounding in why people actually left.
- A retention number is being treated as one problem when it is several.
- Do not use this for how many left at which step. That is `funnel-diagnostics`, with
  `segmentation-analysis` to split cohorts. Return here once they are separated.

## Gather first

1. The churn event under study: cancel, downgrade, non-renewal, or dormant with an active plan.
2. Recency window available and how the list is pulled from `~~data warehouse`.
3. Whether payment-failure churn is separable in the data.
4. The decision this informs, and who owns retention.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Split the cohort before recruiting, not during analysis.** Pull payment-failure churn out
   first as its own list. Then split voluntary churn by the outcome signal in `~~data warehouse`
   or `~~product analytics`: activity spike then stop, gradual decay, or never activated.
   Recruit against all three and label every session.
2. **Recruit within 14 to 30 days of the event, and never ambush.** Contact once, from a named
   researcher, saying plainly that they cancelled, that nothing they say affects their account,
   and that the incentive is unconditional and already theirs. No retention offer in the invite
   or the session. A win-back pitch dressed as research burns the sample. Route wording through
   `participant-comms` and the incentive through `incentives-and-consent`.
3. **Sort outcome from failure in the first five minutes.** Ask "where are you with the thing
   you signed up to do?" before anything about the product. Take the two poles from the churn
   entry in the known problem areas of the product context, which names the outcome that counts
   as the user succeeding: reaching it is outcome-churn, leaving while still pursuing it is
   failure-churn. Label the session before continuing. Someone
   who succeeded is not a defect, and interviewing them about flaws manufactures findings.
4. **Reconstruct the timeline backwards to the last good moment.** Start at the cancellation and
   walk back: "What did you do right before you cancelled? And before that? When was the last
   time this was actually working for you?" The last good moment and the first bad one bracket
   the real story. Common mistake: starting at signup, which lets the participant narrate a
   tidy summary instead of recalling the sequence.
5. **Separate trigger from cause.** The trigger is proximate: a renewal email, a charge, a bad
   output, a friend's advice. The cause is what made the trigger fatal. Ask "had that happened
   before? What was different this time?" A renewal notice triggers everyone, so the cause is
   what distinguishes those who stayed. Report both separately.
6. **Branch payment failure out of the interview entirely.** Involuntary churn is an operations
   problem wearing a churn costume: expired card, issuer decline, currency friction, a dunning
   email in spam. Run five short interviews covering only whether they knew, whether they meant
   to continue, and what they saw. Everything else comes from `~~support desk` records and the
   billing team. Never pool those five into a voluntary churn count.
7. **Ask about returning without selling.** "What would have to be true for you to come back?"
   then "what would have to be happening in your life?" In episodic products the second answer is
   usually the real one, and it is a timing signal, not a feature request. Close there. No
   discount, no promotion, no reconsideration ask.
8. **Treat the form reason as a hypothesis.** Late in the session, read them their own selected
   reason and ask what it left out. The gap between form and story is the finding.

## Output

```
# Churn Study: <window>
Churn event · cohorts recruited · n per cohort · recency · exclusions

## Cohort labels
Outcome-churn | Failure-churn | Never-activated | Payment-failure (separate)

## Guide
Where they are with the goal (label here) · timeline backwards to last good moment ·
trigger vs cause · form-reason gap · return conditions · close

## Payment-failure short guide
Did they know · did they intend to continue · what they saw

## Reporting rules
Counts always by cohort · trigger and cause reported separately · no blended churn rate
```

## Quality bar

- Outcome-churn and failure-churn are separated before the first interview, not after.
- Payment-failure sessions are a distinct guide and never pooled into voluntary churn counts.
- The timeline runs backwards from the event to the last good moment.
- Every reported reason is labelled trigger or cause, and no trigger is reported as a cause.
- No invite or session line contains a retention offer, a discount, or a reconsideration ask.
- Nothing in the guide implies the participant failed, and the incentive is unconditional.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now. Mandatory gate: churned users are a recently harmed
  population and the guide is not fielded until it clears.
- Invoke `incentives-and-consent` next unless the user redirects, then `participant-comms` for
  the non-ambush invite.
- Invoke `interview-moderation` next unless the user redirects, and `session-debrief` after
  every session.
- If the cohorts are not yet separable in the data, invoke `segmentation-analysis` or
  `funnel-diagnostics` instead of recruiting.
- If payment-failure churn dominates, invoke `insight-to-spec` to raise it as an operations
  ticket.
