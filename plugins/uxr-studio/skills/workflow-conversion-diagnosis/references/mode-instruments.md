# Routing failure modes to instruments

Read `${CLAUDE_PLUGIN_ROOT}/context/product.md` first. The modes themselves come from its known
problem areas and from ticket categories in `~~support desk`, never from the analyst's intuition.

## The routing test

Ask two questions of every mode.

1. **Did the user reach the decision?** If yes, the mode is a choice and needs a person to explain
   it. If no, the mode is a block and needs instrumentation, replay or a task-based test.
2. **Is the user the actor?** If the failure is executed by a system (billing, delivery,
   provisioning, eligibility), it is not a user-behavior problem and no interview will improve it.

| Answer pattern | Instrument | Owner |
|---|---|---|
| Reached decision, chose not to proceed | `churn-interview` | research |
| Reached the step, could not understand or operate it | `usability-test-plan` | research |
| Reached the step, hit an error or dead-end state | `funnel-diagnostics` follow-up on error and replay data | research plus engineering |
| System-executed failure | operations handoff via `insight-to-spec` | the owning team |
| Mode suspected but not queryable | `segmentation-analysis` to size it before studying it | research |

## Recruiting traps per mode

- **Abandoners.** Recruit from the mode's cohort, not from current users, and inside the window
  where the decision is still remembered. Reconstruction past that window produces tidy reasons.
- **Blocked users.** They often do not know they were blocked. Ask them to attempt the task rather
  than to recall it.
- **Mixed recruits.** One session pool covering two modes yields an averaged story. Recruit and
  analyze each mode separately even when the sessions look identical.
- **Geography and payment status.** Apply the sampling trap named in the product context. Any mode
  whose share is drawn from raw traffic will be misweighted.
- **Tone.** Apply the participant-facing language rules in the product context. People who failed
  at a step are being asked about a failure, and the wording decides whether they explain or defend.

## Sizing

Every mode needs a share and an absolute count from `~~data warehouse`. A mode with a mechanism
and no size cannot be prioritized. A mode with a size and no mechanism cannot be fixed. Report
both or label which is missing.
