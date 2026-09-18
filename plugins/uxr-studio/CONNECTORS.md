# Connectors

## How tool references work

Skills and agents in this plugin refer to external tools by **category**, written as
`~~category`, rather than by product name. `~~research repository` means whatever repository
you actually use. Nothing breaks if a category has no tool connected: the skill will ask you
for the input instead of fetching it.

Replace the placeholders with your own tool names when you customize the plugin, or connect an
MCP server for the category and leave the placeholders alone.

## Categories used by this plugin

| Category | Placeholder | What the skills use it for | Common options |
|---|---|---|---|
| Product analytics | `~~product analytics` | Funnel steps, feature adoption, session recordings | PostHog, Amplitude, Mixpanel, Heap |
| Data warehouse | `~~data warehouse` | Governed metrics, cohorts, revenue, retention | BigQuery, Snowflake, Redshift |
| Research repository | `~~research repository` | Past studies, transcripts, prior insights | Dovetail, Condens, Notion, Confluence |
| Support desk | `~~support desk` | Tickets, cancellation reasons, refund requests | Zendesk, Intercom, Front |
| Survey tool | `~~survey tool` | Fielding and collecting surveys | Qualtrics, Typeform, SurveyMonkey |
| Review sites | `~~review sites` | Public reviews, app store feedback | G2, Trustpilot, App Store, Play Store |
| Recruiting panel | `~~recruiting panel` | Sourcing and scheduling participants | User Interviews, Respondent, Prolific |
| Scheduling tool | `~~scheduling tool` | Session booking and reminders | Calendly, Google Calendar |
| Transcription tool | `~~transcription tool` | Session recordings and transcripts | Grain, Otter, Zoom |
| Design tool | `~~design tool` | Prototypes and stimuli for testing | Figma |
| Project tracker | `~~project tracker` | Handing findings to delivery teams | Linear, Jira, Asana |
| Chat | `~~chat` | Toplines, shareout invites, newsletters | Slack, Microsoft Teams |

## Which ones matter most

The plugin is fully usable with none of these connected. If you connect only three, connect
`~~research repository` (so `prior-evidence-check` can actually check), `~~product analytics`
(so `funnel-diagnostics` has data), and `~~support desk` (the cheapest qualitative corpus most
teams already own and never read).
