---
name: assumption-mapper
description: >
  This skill should be used when the user asks to "map our assumptions", "what are we
  assuming here", "riskiest assumption test", "de-risk this plan", or needs the beliefs
  behind a product plan surfaced and ranked before research is scoped. Produces a ranked
  assumption map with a research-worthy top band and a do-not-research remainder.
metadata:
  version: "0.1.0"
  stage: "intake"
---

# Assumption Mapper

Rank assumptions on damage if wrong multiplied by lack of confidence that you are right, and
research only the top band. Never rank on how interesting the assumption is, which is the
default failure mode and the reason research backlogs fill with pleasant curiosities while the
load-bearing belief goes untested.

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` before producing anything. It defines the
product, the segments, the sampling traps, and the participant-facing language rules.

## Use this when

- A feature, launch, or bet is being planned and nobody has written down what it depends on.
- A roadmap item keeps getting debated and the disagreement is really about an untested belief.
- A study is being scoped and you need to know which belief it should attack.
- Do not use this when the team already agrees on the risky belief and needs a study designed.
  Go straight to `research-brief-builder`.

## Gather first

1. The plan, bet, or feature in one paragraph.
2. Who benefits and what they must do differently for it to work.
3. What the team would consider a failed launch, stated as an outcome.

Ask only for what is missing, at most 3 questions in one batch via AskUserQuestion. Infer the
rest from the product context and say what you inferred.

## Method

1. **Extract assumptions from the plan sentence by sentence.** Every claim about what users
   will do, what they value, what the business gets, and what can be built is an assumption.
   Write each as a falsifiable declarative naming a population from the segment table in the
   product context, a countable action, and a window: "<segment> will <do the specific thing>
   within 48 hours of <event>." Not "users engage with the feature."
2. **Sort into four types.** Each assumption gets exactly one.
   - **Desirability** — users want the outcome or feel the problem. Tested with qual with real
     users, or with behavioral evidence already in `~~product analytics`.
   - **Viability** — the business gets something. Tested with `~~data warehouse` numbers,
     pricing work, or an experiment, rarely with interviews.
   - **Feasibility** — it can be built, operated, and supported at cost. Tested by engineering,
     not by research. Say so and route it away.
   - **Usability** — people can actually complete it. Tested with `usability-test-plan`.
   Mixing these is why teams run interviews to answer feasibility questions.
3. **Score damage, 1 to 5.** How bad is it if this is false? 5 means the plan dies or users are
   harmed. Anchor on the outcome the team named as failure. Do not score effort here.
4. **Score confidence, 1 to 5.** How sure are we, and on what evidence? 5 means direct
   behavioral evidence in this product, for this segment, in the last year. Opinion from a
   senior person is 2. Analogy to another product is 2. Something everyone repeats but nobody
   sourced is 1, and is usually the most dangerous item on the page.
5. **Rank by damage multiplied by (6 minus confidence).** Sort descending. The top band is
   everything scoring 15 or above, capped at five items.
6. **Research only the top band.** For each item below the band, write one of: accept and
   monitor, assign to engineering, decide by argument, or revisit after launch. Writing the
   disposition matters as much as the ranking, because unranked leftovers come back as
   ad hoc requests.
7. **Check the top band for hidden compounds.** An assumption covering two populations is two
   assumptions. Draw the worked example from the known problem areas in the product context:
   find the standing problem whose headline number the context flags as two unrelated failures
   wearing one label, and show the two populations pulled apart. Split before scoping.
8. **Name the cheapest disconfirming test per top-band item.** The test is the one that could
   show the belief is false, not the one that would produce a nice quote. For several items the
   cheapest test is a query, not a study, and `prior-evidence-check` should run first.

## Output

```
## The plan in one line

## Assumption map
| # | Assumption (falsifiable) | Type | Damage | Confidence | Score | Evidence behind the confidence |

## Top band (research these)
For each: the assumption · what would disconfirm it · cheapest disconfirming test ·
who owns the result · suggested skill

## Everything else
| Assumption | Disposition | Why |

## Loudest unsourced belief
<the item with confidence 1 that the team treats as fact>
```

## Quality bar

- Every assumption is written as a falsifiable declarative with a named population.
- Every assumption has exactly one of the four types, and feasibility items are routed away
  from research.
- Confidence scores cite the evidence behind them, not a feeling.
- The top band has at most five items and every one has a disconfirming test.
- Every non-top-band item has a written disposition.
- No item is ranked up because it is interesting.

If any check fails, revise before showing the user. Do not ship the draft with a caveat.

## Next

Do not stop at the output. Continue the chain in this same turn unless the user redirects.

- Invoke `prior-evidence-check` now on the top band. Mandatory gate: no assumption is scoped
  into a study before existing evidence is swept.
- Invoke `research-brief-builder` next unless the user redirects, for each top-band item that
  survives the sweep.
- If a top-band item is a usability assumption, invoke `usability-test-plan` instead.
- If it is a viability assumption, invoke `ab-test-design` or `pricing-sensitivity` instead.
- Invoke `research-roadmap` next unless the user redirects, to sequence the surviving studies.
- Stop here if the top band is empty or every item routed to engineering.
