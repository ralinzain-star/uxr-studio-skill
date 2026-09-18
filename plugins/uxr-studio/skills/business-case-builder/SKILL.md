---
name: business-case-builder
description: >
  This skill should be used when the user asks to "make the case for a researcher", "justify a
  research tool", "get budget for a panel", "write a business case for research", or needs to
  argue for research investment to a budget holder. Produces a three-option case argued from the
  cost of decisions currently made without evidence, with objections pre-answered.
metadata:
  version: "0.1.0"
  stage: "impact"
---

# Business Case Builder

Argue from the cost of the decisions this organization is currently making without evidence.
Do not argue from best practice, maturity models, or what peer companies staff. Those are
arguable, generic, and easy to defer. A list of this quarter's blind decisions, with their build
cost attached, is specific to this company and hard to wave off.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A headcount, tool, panel subscription, or research program needs funding approval.
- A research function is being cut or questioned and needs a defense.
- Planning season is open and research has to compete for a slot.
- Do not use this when the task is valuing research already delivered. Use `research-roi` and
  bring its number here as one input.

## Gather first

1. The specific ask: what, how much, for how long, and what it replaces.
2. The decisions coming in the next two quarters, from the decisions section of the product
   context and the roadmap.
3. The last two quarters of decisions made without evidence, and what each one cost to build.
4. Who signs, what they were burned by last time, and their unit of account.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Size the evidence gap first, before mentioning the ask.** List the decisions made in the
   last two quarters with no research behind them. For each, record the build cost in team-weeks
   and whether it worked. Count how many are still unresolved or were reversed. This table is
   the case. Everything after it is implementation detail.
2. **Anchor the gap to the standing problem areas in the product context.** Show which of them
   have decisions attached and no evidence attached. A gap that maps to the company's own named
   problems cannot be dismissed as researcher self-interest.
3. **Convert the gap to money in the same team-week unit as `research-roi`.** One rate, stated
   once, used everywhere.
4. **Structure three options and make the recommendation the obvious middle.** Two options force
   a yes or no. Three reframe the conversation as which, and the middle wins by shape.
   **Hard stop: if the budget holder is not known by name, do not continue.** Ask for them with
   AskUserQuestion and wait. Never infer the signer, never write "TBD", and never proceed
   assuming they will be identified later. Option B is sized to what a specific person will
   fund and priced in their unit of account, so a case written for nobody is a document that
   circulates and never gets signed. The only exception is an explicitly unattended run, in
   which case emit the verdict `Blocked: no named budget holder` and nothing else.
   - **Option A, do nothing.** Not a strawman. State honestly what still gets answered without
     the investment, using `~~support desk`, `~~product analytics`, and existing evidence.
     Then state what stays blind.
   - **Option B, the recommendation.** The smallest version that closes the named gap. Scope,
     cost, what it covers, and what it explicitly does not.
   - **Option C, the full version.** Real, costed, and genuinely better. It exists to set the
     ceiling, so do not inflate it into a joke. A fake Option C discredits Option B.
5. **Pre-answer five objections in the document, not in the meeting.** Three sentences each.
   "Can't sales and support tell us this." "Why not just run a survey." "Why now rather than next
   half." "What if the questions dry up after two quarters." "How will we know it worked."
   An objection answered in writing is settled. The same objection raised live becomes a debate
   the budget holder has to adjudicate in front of peers.
6. **Write the do-nothing section as dated decisions, not as risk.** Name the next three
   decisions that will be made blind, their dates, and their build cost. Risk language is
   discountable. A dated decision with a number is not.
7. **Commit to a success measure and a review date before approval, not after.** One metric,
   one date, one named owner, set up through `decision-metrics-mapper` and logged in
   `impact-tracker`. Volunteering the kill criterion is what makes the ask look disciplined.
8. **Keep the case to two pages.** Length reads as weakness in a budget review. Detail goes in
   an appendix nobody has to open.

## Output

- **The ask** — one sentence: what, how much, how long, who owns it.
- **The evidence gap** — the table of blind decisions, cost, outcome, and unresolved count.
- **What this closes** — the gap rows Option B removes, mapped to problem areas from the
  product context.
- **Three options** — A, B, C, each with scope, cost, and what it leaves uncovered.
- **Objections** — five, each answered in three sentences.
- **If we do nothing** — three dated upcoming decisions and their build cost.
- **How we will know** — the metric, the review date, the owner, and the kill criterion.
- **Appendix** — rates, assumptions, and any `research-roi` output used.

## Quality bar

- The evidence gap table precedes the ask and cites real decisions with real costs.
- No sentence argues from industry benchmarks, maturity models, or peer-company staffing.
- Option A is stated fairly and Option C is genuinely viable at its price.
- All five objections are answered in the document in three sentences or fewer each.
- The do-nothing section names dated decisions, not categories of risk.
- A kill criterion and a review date are stated before approval is requested.
- The main case fits two pages.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- If no value number exists yet, invoke `research-roi` first and bring its range into the gap
  table.
- Invoke `decision-metrics-mapper` next unless the user redirects, to set the one success
  metric, the review date, and its owner.
- Invoke `impact-tracker` next unless the user redirects, to register that review date against
  the decision the case turns on.
- If the case is declined, invoke `research-roadmap` instead, to re-sequence what current
  capacity can still cover.
- Stop here once the case is with the budget holder and the review date is logged.
