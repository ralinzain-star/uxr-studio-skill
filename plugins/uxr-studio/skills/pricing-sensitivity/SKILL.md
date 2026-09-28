---
name: pricing-sensitivity
description: >
  This skill should be used when the user asks "what should we charge", "run a willingness to
  pay study", "test a price increase", or needs to understand how demand responds to price
  before changing a plan. Produces a price-sensitivity battery, segment-level acceptable ranges,
  and a required live pricing experiment to validate the number.
metadata:
  version: "0.1.0"
  stage: "quant"
---

# Pricing Sensitivity

Stated willingness to pay is systematically inflated, because nobody's card is charged in a
survey. Never report a survey output as a price. These methods find the shape of the demand
curve and the boundaries of acceptability. The number itself comes from a live test, and this
skill is not finished until that test is specified.

Read the product context before producing anything: `.claude/uxr-product.md` in the current
project if it exists, otherwise `${CLAUDE_PLUGIN_ROOT}/context/product.md`. It defines the
product, the segments, the sampling traps, and the participant-facing language rules. If
neither file exists, say so once, then ask only for the product details this task needs,
using the headings in `${CLAUDE_PLUGIN_ROOT}/context/product.template.md` as the checklist.

## Use this when

- A plan price, trial length or packaging change is being considered and nobody knows the range.
- A new plan tier needs a starting price to test.
- A price increase is proposed and you need the acceptability boundary before risking revenue.
- Do not use this when the question is which features belong in which tier. Use
  `maxdiff-and-tradeoff` first, then price the resulting packages here.

## Gather first

1. The pricing decision on the table, including current prices, intervals and tiers.
2. Current conversion, churn and revenue per account by plan from `~~data warehouse`.
3. Which segments must be read separately, and whether the paying population is large enough.
4. Whether a live pricing experiment is operationally possible, and who owns it.

Ask only for what is missing, at most 4 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Describe the offer before asking about price.** Show exactly what is included at the
   interval being priced. Respondents price whatever they imagine, so an unspecified offer
   produces an unusable spread.
2. **Field the four-question price-sensitivity battery.** In order: at what price would this be so
   low you would question the quality, at what price would it be a bargain, at what price would it
   start to feel expensive, and at what price would it be too expensive to consider. Use an open numeric
   field with the currency stated, never a scale or a price list, and keep the too-cheap question
   first so nobody is anchored by their own ceiling. Plot the cumulative curves to read the
   acceptable range and the marginal points. The band is the output, not its midpoint.
3. **Pair every price point with a purchase-intent item.** After the battery, show two prices
   drawn from the respondent's own answers and ask likelihood to purchase on a five-point scale.
   Without it you have acceptability with no demand attached, which is how teams price at the
   widest point of a curve nobody buys on. Only "definitely would" carries signal, and it
   overstates.
4. **Price the interval as a product, not as a discount.** A long-interval plan is a different
   purchase from a short-interval one: different commitment, risk and cash outlay. Never field it as
   a multiple of a shorter one less a discount. Each interval gets its own described offer and
   its own battery. Take the interval and trial shape from the funnel section of the product
   context and price the interval that carries the commitment. Where a trial sits in front of a
   longer interval, the real decision is an up-front commitment made before the user knows the
   product works, and a shorter-interval price tells you almost nothing about it.
5. **Recruit from people who could actually pay.** Trialing and paying users, never raw traffic.
   Use the sampling trap named in the product context as the worked example: it describes exactly
   the population a conveniently drawn sample over-weights, which depresses every curve and yields
   a revenue-destroying price. State the sample's geography in the deliverable.
6. **Read the curves per segment, not just overall.** Expect at least two shapes: a high-urgency
   segment with a wide band and a low-urgency one with a narrow band and a low ceiling. Averaging
   them prices for neither. Budget 200 or more per segment, and say so when you do not have it.
   Take the pair to contrast from the segment table in the product context, choosing the two
   segments whose urgency differs most. They will not share a ceiling.
7. **Convert to a testable hypothesis, not a price.** Output a candidate range and a single
   hypothesis: "at price P, conversion falls by no more than X% and revenue per visitor rises."
   Anything stated as a recommended price is out of scope for this method.
8. **Specify the live experiment and make it a blocker.** Name the cells, the runtime, the cohort
   maturity, and the primary metric as revenue per eligible visitor rather than conversion rate.
   Check the feedback-loop length in the funnel section of the product context before setting the
   runtime. Where the renewal signal arrives after the test window, a short test reads conversion
   only and cannot claim revenue impact. State plainly that no price changes before this reads
   out.
   **Hard stop: if no named owner has confirmed the live pricing experiment will actually
   run, do not continue.** Ask with AskUserQuestion and wait. Never infer an owner, never
   write "TBD", and never proceed assuming the experiment will be arranged later. A stated
   range shipped without a live test behind it becomes the price, which is the exact failure
   this method exists to prevent. The only exception is an explicitly unattended run, in which
   case emit the verdict `Blocked: no owner for the live pricing experiment` and nothing else.

## Output

- **Decision and current state** — prices, intervals, tiers, conversion, churn.
- **Offer description as fielded** — the exact wording shown, per interval.
- **Battery** — the four questions verbatim, plus the purchase-intent items and prices.
- **Sample** — source population, n, n per segment, geography statement.
- **Curves** — acceptable range and marginal points, overall and per segment, with intent.
- **Candidate price range** — a band, never a point, with the inflation caveat stated.
- **Testable hypothesis** — the single sentence from step 7.
- **Required live experiment** — cells, metric, runtime, maturity, owner, decision rule.

## Quality bar

- No single recommended price appears anywhere in the deliverable.
- Each interval was fielded as its own described offer, never as a discount off another.
- Purchase intent is attached to at least two price points per respondent.
- The sample is drawn from paying or trialing users and its geography is stated.
- Segment-level curves are reported, or the deliverable says the sample could not support them.
- The live experiment section is complete enough to hand to an engineer today.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `research-ethics-review` now unless it has already cleared this study. It is a
  mandatory gate: nothing fields before the harm review.
- Invoke `survey-builder` next unless the user redirects, then `survey-analysis` when the data
  lands.
- Invoke `ab-test-design` now with the experiment spec. It is not optional: this skill produced
  a range, and no price moves until a live test reads out.
- If the stated range conflicts with cancellation reasons, invoke `triangulation`; if revenue
  must be argued, invoke `business-case-builder`.
