# uxr-studio

A complete user-research practice packaged as a Claude Code plugin: **9 stage agents**,
**64 opinionated method skills**, and **7 end-to-end workflows** that take a study from a vague
stakeholder request to a decision that actually moved.

```
intake → scoping → recruiting → qual / quant → synthesis → reporting → impact
                                  ↑ ops (repository, ethics, retros) runs alongside ↑
```

## Why this exists

Most research prompts give you a menu of methods. This plugin gives you a *practice*: a
consistent point of view about how research should be done, applied the same way every time.

- **Every skill is opinionated.** Each teaches one specific way to do the thing and says why.
  Surveys do not answer "why". Small usability tests do not produce rates. Insights that cannot
  be wrong get killed before they reach a report.
- **Agents have refusals, not just capabilities.** The recruiting agent will not make an
  incentive conditional on completion. The quant agent will not report a rate from a small
  qualitative sample. The reporting agent will not ship a finding that fails the so-what gate.
- **No product knowledge is baked in.** Everything product-specific lives in one file,
  `context/product.md`. Swap that file and the whole practice re-aims at a different product
  without editing a single skill.

## Install

From inside Claude Code:

```
/plugin marketplace add ralinzain-star/uxr-studio-skill
/plugin install uxr-studio@uxr-studio-marketplace
```

Or from your shell:

```bash
claude plugin marketplace add ralinzain-star/uxr-studio-skill
```

```bash
claude plugin install uxr-studio@uxr-studio-marketplace --scope user
```

Then run `/reload-plugins` — it should report 64 skills and 9 agents.

To customise the product context (recommended), clone the repo and install from the local
folder instead, or try it for a single session without installing:

```bash
git clone https://github.com/ralinzain-star/uxr-studio-skill.git
```

```bash
claude --plugin-dir ./uxr-studio-skill/plugins/uxr-studio
```

See [INSTALL.md](INSTALL.md) for local-marketplace install, validation, and troubleshooting.

## Set up your product context

The skills read one file for everything they need to know about your product. It is gitignored
because it holds real company data, so create your own:

```bash
cp plugins/uxr-studio/context/product.template.md plugins/uxr-studio/context/product.md
```

Fill in each section and keep the headings — skills look them up by name:

| Section | What it gives the skills |
|---|---|
| Product | What it is and who pays |
| Who the users are | Segment table plus a named sampling trap |
| Features to research by name | Consistent vocabulary across studies |
| The funnel | Steps and feedback-loop length |
| Known problem areas | The standing research agenda |
| Language and tone rules | Guardrails for anything participant-facing |
| Where evidence already lives | So `prior-evidence-check` knows where to look |
| Decisions research is expected to inform | What every study must tie back to |

A thin file still works — the skills just ask more questions. The richer it is, the less
generic the output.

## Quick start

```
/uxr-studio:method-selector
/uxr-studio:research-intake-triage
/uxr-studio:workflow-conversion-diagnosis
```

Or address an agent directly, e.g. *"Hand this to uxr-intake: can we run a survey on pricing
next sprint?"*

| If you… | Start with |
|---|---|
| Don't know which method to use | `method-selector` |
| Were asked to "just run a survey" | `research-intake-triage`, then `method-selector` |
| Have data and need findings | `affinity-synthesis` or `thematic-coding`, then `insight-writer` |
| Are about to send a report | `so-what-checker` — on everything, not just weak drafts |
| See users dropping out somewhere | `workflow-conversion-diagnosis` |
| Need to justify the research function | `workflow-impact` |

## What's inside

### Agents

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

### Workflows

Each workflow chains existing skills, names what passes between steps, marks what can run in
parallel, gives a realistic elapsed time, and includes at least one abort gate.

| Workflow | Use it to | Elapsed time |
|---|---|---|
| `workflow-discovery` | Run generative research when the problem is unknown | 5–7 weeks |
| `workflow-usability` | Evaluate a design against tasks | 2–3 weeks |
| `workflow-mixed-methods` | Answer one question with qual and quant together | 4–8 weeks |
| `workflow-report` | Turn completed analysis into a recorded decision | 4–7 days |
| `workflow-ia` | Fix an information architecture | 5–6 weeks |
| `workflow-impact` | Prove and grow the research function | Quarterly |
| `workflow-conversion-diagnosis` | **Flagship.** Split a funnel drop into its failure modes and fix each | 6–8 weeks |

### Skills by stage

<details>
<summary>All 57 method skills</summary>

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

</details>

## Connecting your tools

Skills refer to tools by category — `~~product analytics`, `~~research repository`,
`~~support desk` — rather than by product name, so they work with whatever stack you use.
**Nothing needs to be connected**: when a category has no tool, the skill asks you for the
input instead.

If you connect only three, connect a research repository, product analytics, and your support
desk. See [CONNECTORS.md](plugins/uxr-studio/CONNECTORS.md) for all twelve categories.

## Repository layout

```
.claude-plugin/marketplace.json     # makes this repo installable as a marketplace
plugins/uxr-studio/
├── .claude-plugin/plugin.json
├── agents/                         # 9 stage agents
├── skills/                         # 64 skills (57 methods + 7 workflows)
├── context/product.template.md     # copy to product.md and fill in
├── CONNECTORS.md
└── README.md
```

## Contributing

Issues and pull requests are welcome. New skills should follow the existing pattern: take a
position, say why, name what to gather first, and read `context/product.md` rather than
hard-coding product details.

## License

MIT © Iris Hsieh
