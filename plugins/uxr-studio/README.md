# uxr-studio

A complete user-research practice for Claude: **9 stage agents**, **64 skills**, and **7 workflow
chains** that run a study from a vague stakeholder request to a decision that actually moved.

Two design choices shape everything here:

1. **Every skill is opinionated.** Each one teaches a specific way to do the thing and says why.
   Surveys do not answer "why". Small usability tests do not produce rates. Insights that cannot
   be wrong get killed. The point is that you get a consistent practice instead of a menu.
2. **No product knowledge is baked into the skills.** All of it lives in one file,
   `context/product.md`. Swap that file and the entire practice re-aims at a different product
   without touching a single skill.

## Pointing it at your product

`context/product.md` is gitignored — it holds real company data, so you create your own. To aim it at your product:

- Copy `context/product.template.md` over `context/product.md` and fill it in, or
- Edit `context/product.md` directly.

The skills read these sections by name, so keep the headings: Product · Who the users are
(segment table and a named sampling trap) · Features to research by name · The funnel (with
feedback-loop length) · Known problem areas · Language and tone rules for anything
participant-facing · Where evidence already lives · Decisions research is expected to inform.

The richer that file is, the fewer questions the skills ask you and the less generic their
output. A thin context file still works; the skills just ask more.

## The nine agents

| Agent | Owns | Hands to |
|---|---|---|
| `uxr-intake` | Turning requests into decision-tied work, or declining them | scoping |
| `uxr-scoping` | Method choice, sample, study design | recruiting, qual, quant |
| `uxr-recruiting` | Frame, screener, comms, consent, participant quality | qual, quant |
| `uxr-qual` | Interviews, usability, concept tests, diary, IA studies | synthesis |
| `uxr-quant` | Surveys, experiments, funnels, segmentation, pricing | synthesis |
| `uxr-synthesis` | Debriefs, coding, insights, triangulation, models | reporting |
| `uxr-reporting` | Reports, toplines, shareouts, specs, the backlog | impact |
| `uxr-impact` | ROI, metric chains, business cases, impact tracking | intake |
| `uxr-ops` | Repository, democratization, ethics, retros, audits | everywhere |

Each agent works through four named specialist lanes, invokes the skills in its stage, and has
explicit refusals. The recruiting agent will not make an incentive conditional on completing.
The quant agent will not report a rate from a small qualitative sample. The reporting agent
will not ship a finding that fails the so-what gate.

## The seven workflows

Each chains existing skills end to end, names what passes between steps, marks what can run in
parallel, gives a realistic elapsed time, and includes at least one abort gate where stopping
is the right call.

- `workflow-discovery` — generative research when the problem is unknown. 5-7 weeks.
- `workflow-usability` — evaluate a design against tasks. 2-3 weeks.
- `workflow-mixed-methods` — qual and quant on one question. 4-8 weeks.
- `workflow-report` — completed analysis to a recorded decision. 4-7 days.
- `workflow-ia` — fix an information architecture. 5-6 weeks.
- `workflow-impact` — prove and grow the research function. Quarterly.
- `workflow-conversion-diagnosis` — the flagship. Diagnose a funnel drop, including the split
  check that separates structurally different failure modes hiding inside one aggregate rate.
  6-8 weeks.

## Skills by stage

**Intake (4)** research-intake-triage · research-brief-builder · research-question-sharpener ·
assumption-mapper

**Scoping (5)** method-selector · mixed-methods-designer · sample-size-advisor ·
research-roadmap · prior-evidence-check

**Recruiting (5)** sampling-plan · screener-builder · participant-comms ·
incentives-and-consent · recruiting-quality-check

**Qualitative (10)** interview-guide-builder · interview-moderation · usability-test-plan ·
concept-test-design · churn-interview · diary-study-design · contextual-inquiry ·
card-sort-design · tree-test-design · cocreation-workshop

**Quantitative (9)** survey-builder · survey-analysis · ab-test-design · ab-test-analysis ·
quant-usability-metrics · funnel-diagnostics · segmentation-analysis · maxdiff-and-tradeoff ·
pricing-sensitivity

**Synthesis (8)** session-debrief · affinity-synthesis · thematic-coding · lightning-synthesis ·
insight-writer · triangulation · persona-builder · journey-map-builder

**Reporting (7)** pyramid-report · topline-writer · so-what-checker · research-shareout ·
insight-to-spec · opportunity-backlog · research-newsletter

**Impact (4)** research-roi · decision-metrics-mapper · business-case-builder · impact-tracker

**Ops (5)** research-repository-hygiene · democratization-guardrails · research-ethics-review ·
study-retro · research-process-audit

**Workflows (7)** as listed above.

## Starting points

- Do not know what method to use: `method-selector`.
- Someone asked for a survey: `research-intake-triage` first, then `method-selector`.
- Have data, need findings: `affinity-synthesis` or `thematic-coding`, then `insight-writer`.
- About to send a report: `so-what-checker`. Run it on everything, not just weak drafts.
- Users are dropping out somewhere: `workflow-conversion-diagnosis`.
- Need to justify the research function: `workflow-impact`.

## External tools

Skills refer to external tools by category (`~~product analytics`, `~~research repository`,
and so on) rather than by product name. The plugin is fully usable with none of them connected.
See `CONNECTORS.md` for the full list and which three are worth connecting first.
