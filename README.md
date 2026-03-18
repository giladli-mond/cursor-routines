# Cursor Routines

A system for automating daily and weekly routines using Cursor AI agents. Built for product managers and anyone who wants their AI assistant to run structured morning briefings, end-of-day summaries, weekly reviews, and more.

> **Internal version:** This repo includes monday.com tool references (Kremer MCP, monday AI notetaker, Slack channel conventions). For the fully genericised public version, see [cursor-routines on GitHub](https://github.com/gililivnat/cursor-routines).

## What This Does

Every time you start a Cursor conversation, the system checks a state file to see which routines are due. It runs each step as a dedicated **skill** (a SKILL.md file the agent reads and follows), tracks progress, and avoids repeating work already done today.

The core idea is simple:
- A **flag-based state machine** tracks what's been done today
- Each routine step is a **standalone skill** that can be run independently or improved over time
- The agent resets the flags each morning and works through uncompleted steps

Beyond routines, the repo includes **utility skills** you can trigger any time — creating presentations, preparing for customer interviews, and running voice-of-the-customer analysis.

## Quick Start

```bash
git clone git@github.com:giladli-mond/cursor-routines.git
```

Open the repo in Cursor and say **"onboarding"**. The agent will walk you through setup — asking about your Slack ID, product name, connected MCPs, and workspace layout — then configure everything automatically.

If you prefer manual setup, see [Manual Setup](#manual-setup) below.

## How It Works

```
Cursor Rule (always-on)
  → reads ORCHESTRATOR.md
    → reads state.md (date + flags)
      → if new day: reset flags
      → if flags incomplete: run skills in order
        → each skill reads its SKILL.md, executes, marks itself done
      → if all done: skip
    → checks scheduled tasks
```

### The Flag Mechanism

The `state.md` file has YAML frontmatter tracking completion:

```yaml
date: "2026-03-18"
daily_start:
  flag: 0                        # 0 = not done, 1 = all steps complete
  review_slack_dms: false         # individual step tracking
  review_campaigns_channels: false
  ...
```

**Logic:**
1. If `date` < today → reset all daily flags to `false`, set `date` to today
2. If `date` == today and `flag` == 1 → skip (already done)
3. If `date` == today and `flag` == 0 → run steps still marked `false`
4. After each step completes → mark it `true`
5. When all steps are `true` → set the group `flag` to `1`

If a session crashes mid-routine, the next session picks up where it left off.

## Skills Reference

### Routine Skills

These run automatically as part of daily/weekly routines.

| Skill | What it does | Required MCPs | Cadence |
|-------|-------------|---------------|---------|
| **review-slack-dms** | Scans your DMs and threads, flags what needs a response | Slack | Daily AM |
| **review-campaigns-channels** | Reads all Slack channels matching your keyword, summarises activity | Slack | Daily AM |
| **summarise-slack-and-suggest-tasks** | Consolidates Slack findings into a morning briefing (HTML), suggests actionable Todoist tasks | Slack, Todoist (optional) | Daily AM |
| **review-daily-metrics** | Queries Kremer/Snowflake, surfaces highlights and anomalies (HTML output) | Kremer | Daily AM |
| **review-upcoming-meetings** | Reviews today's calendar and prepares context | Calendar | Daily AM *(placeholder)* |
| **review-gmail** | Scans inbox for urgent items | Gmail | Daily AM *(placeholder)* |
| **summarise-campaigns-day** | Reviews product Slack messages and threads, highlights decisions and open items (HTML output) | Slack | Daily PM |
| **review-notetaker-meetings** | Checks monday AI notetaker for today's meetings, extracts action items (HTML output) | Monday API | Daily PM |
| **summarise-week** | Consolidates the week's activity, metrics, and meetings; drafts Slack messages for team, company, and directors | Slack, Kremer | Weekly (Fri) |
| **plan-week** | Reviews carry-overs, scheduled tasks, sets top 3 priorities (HTML output) | Slack | Weekly (Mon) |
| **plan-quarter** | Reviews OKRs, drafts next quarter priorities (HTML output) | Kremer | Quarterly |

### Utility Skills

These are triggered on demand — just ask for them.

| Skill | What it does | Required MCPs |
|-------|-------------|---------------|
| **create-presentation** | Builds interactive HTML presentations with charts and animations | Kremer (optional) |
| **prepare-customer-interview** | Researches a customer before an interview — usage data, Slack history, website | Kremer, Slack |
| **voice-of-the-customer** | Aggregates insights from 6 sources (usage data, ask-campaigns, churn, Reddit, Mixpanel, internal Slack) into a themed VoC report | Slack, Kremer, Mixpanel (optional) |
| **onboarding** | Interactive setup wizard that configures the whole system | None |

## Routines

### Daily: Start of Day

1. **Review Slack DMs** — scan personal messages and threads you're part of, flag what needs a response
2. **Review product channels** — read all Slack channels matching your keyword, check threads you're in, summarise activity
3. **Summarise + suggest tasks** — consolidate Slack findings into a morning briefing (HTML), suggest actionable tasks
4. **Review daily metrics** — query Kremer/Snowflake, surface highlights and anomalies (HTML output)
5. **Review upcoming meetings** *(placeholder)* — needs a Calendar MCP
6. **Review Gmail** *(placeholder)* — needs a Gmail MCP

### Daily: End of Day

1. **Summarise the day** — review product Slack messages and threads, highlight decisions and open items (HTML output)
2. **Review meeting notes** — check monday AI notetaker for today's meetings, extract action items (HTML output)

### Weekly

- **Summarise the week** (Friday) — consolidate activity, metrics, meetings into HTML report; draft Slack messages for team, company, and directors
- **Plan the week** (Monday) — review carry-overs, scheduled tasks, set top 3 priorities (HTML output)
- **Plan the quarter** (last month of Q) — review OKRs, draft next quarter priorities (HTML output)

## Key Features

- **Thread tracking:** DM and channel review skills check for threads you're part of where someone has replied since your last message, flagging conversations that need a quick response
- **HTML output:** Morning briefings, EOD summaries, weekly reports, and plans are generated as styled HTML files with dark theme, metric cards, priority lists, and interactive Todoist/Slack buttons — easily customisable via the shared template
- **Slack message drafting:** Weekly summaries draft three separate Slack messages (team, company, directors) with appropriate tone and length for each audience
- **Task suggestions:** Morning briefing consolidates Slack activity and suggests actionable Todoist tasks
- **Idempotent execution:** If a session crashes mid-routine, the next session picks up where it left off
- **Progressive activation:** Placeholder skills let you define the full routine now and activate steps as you connect new tools

## Structure

```
cursor-routines/
├── README.md
├── config.example.yaml              — template config (onboarding generates config.yaml)
├── cursor-rule-example.md           — example Cursor rule to activate the system
├── routines/
│   ├── ORCHESTRATOR.md              — flag logic and execution rules
│   ├── state.example.md             — template state file (copy to state.md)
│   ├── daily/
│   │   ├── start-of-day.md
│   │   └── end-of-day.md
│   └── weekly/
│       ├── week-summary.md
│       ├── week-planning.md
│       └── quarterly-planning.md
└── skills/
    ├── onboarding/                  — interactive setup wizard
    ├── review-slack-dms/
    ├── review-campaigns-channels/
    ├── summarise-slack-and-suggest-tasks/
    ├── review-daily-metrics/
    ├── review-upcoming-meetings/         (placeholder)
    ├── review-gmail/                     (placeholder)
    ├── summarise-campaigns-day/
    ├── review-notetaker-meetings/
    ├── summarise-week/
    ├── plan-week/
    ├── plan-quarter/
    ├── create-presentation/
    ├── prepare-customer-interview/
    ├── voice-of-the-customer/
    └── system/
        ├── routine-html-template.md      — shared CSS/HTML template for routine outputs
        └── presentation-html-template.md — shared template for presentations
```

## Setup

### Recommended: Onboarding

The fastest way to get started. Clone the repo, open it in Cursor, and say **"onboarding"**.

The onboarding skill will:
1. Ask about your workspace layout, product name, and Slack channels
2. Collect your Slack user ID and MCP server names
3. Replace all placeholders across skill files with your actual values
4. Generate `config.yaml`, `state.md`, and the Cursor rule
5. Optionally install skills to `~/.cursor/skills/` for auto-discovery
6. Create output directories for reports and summaries

Run it again any time to reconfigure.

### Manual Setup

If you prefer to set things up by hand:

#### 1. Copy the files into your workspace

Place the `routines/` and `skills/` folders wherever makes sense in your project structure. Update the file paths in the orchestrator and routine files accordingly.

#### 2. Create your state file

```bash
cp routines/state.example.md routines/state.md
```

Edit the date and fields to match today and your active skills.

#### 3. Install the Cursor rule

Copy `cursor-rule-example.md` to `.cursor/rules/routines.mdc` (or whatever naming convention you use). Replace `[path-to]` with the actual path to your routines folder. This tells Cursor to check routines at the start of every conversation.

#### 4. Install skills to Cursor

For Cursor to auto-discover skills, copy each SKILL.md to `~/.cursor/skills/[skill-name]/SKILL.md`:

```bash
for skill in skills/*/; do
  name=$(basename "$skill")
  [ "$name" = "system" ] && continue
  mkdir -p ~/.cursor/skills/$name
  cp "$skill/SKILL.md" ~/.cursor/skills/$name/SKILL.md
done
```

#### 5. Configure for your product

Replace these placeholders across the skill files:

| Placeholder | What to set | Where it appears |
|-------------|------------|-----------------|
| `YOUR_SLACK_USER_ID` | Your Slack member ID (starts with `U`) | review-slack-dms, review-campaigns-channels, summarise-campaigns-day |
| `CHANNEL_KEYWORD` | Keyword matching your product's Slack channels | review-campaigns-channels, summarise-campaigns-day |
| Channel search keyword | Match your product's Slack channels | review-campaigns-channels, summarise-campaigns-day |
| monday AI notetaker tool names | Confirm they match your MCP setup | review-notetaker-meetings |
| Slack Channel IDs | Your team's Slack channels for weekly summaries | summarise-week |

#### 6. Connect required MCPs

The active skills use these monday.com Cursor MCP connections:

| MCP Server | Status | Used by |
|------------|--------|---------|
| **Slack MCP** (`user-slack`) | Required (core) | review-slack-dms, review-campaigns-channels, summarise-slack-and-suggest-tasks, summarise-campaigns-day, voice-of-the-customer, prepare-customer-interview |
| **Kremer MCP** (`user-kremer-mcp`) | Recommended | review-daily-metrics, voice-of-the-customer, create-presentation, prepare-customer-interview, plan-quarter |
| **Monday API MCP** (`user-monday-api-mcp`) | Optional | review-notetaker-meetings, plan-week |
| **Todoist MCP** (`user-todoist`) | Optional | summarise-slack-and-suggest-tasks |
| **Mixpanel MCP** (`user-mixpanel`) | Optional | voice-of-the-customer, prepare-customer-interview |
| **Google Calendar MCP** | Placeholder | review-upcoming-meetings |
| **Gmail MCP** | Placeholder | review-gmail |

The system works with just a Slack MCP. Skills that require unavailable MCPs are skipped gracefully.

## Configuration

The `config.yaml` file (generated by onboarding) stores all your settings in one place:

```yaml
product_name: "My Product"
channel_keyword: "product"
slack_user_id: "U01ABC2DEF3"
data_warehouse_mcp: "user-kremer-mcp"
output_path: "Output"
# ... see config.example.yaml for all fields
```

This file is primarily a reference — the actual configuration lives in the skill files themselves (where placeholders get replaced with your values). If you re-run onboarding, it reads `config.yaml` first and only asks about fields you want to change.

## Customising

### Adding a new routine step

1. Create a new skill: `skills/[name]/SKILL.md`
2. Add a field for it in `state.md` under the relevant group
3. Add a step entry in the relevant routine file (`daily/start-of-day.md`, etc.)
4. Add it to the skills registry in `ORCHESTRATOR.md`

### Removing a routine step

1. Comment out or remove the field from `state.md`
2. Remove the step from the routine file
3. Optionally delete the skill folder

### Changing cadence

Edit the routine files and the orchestrator's "Determine what to run" logic. For example, to make weekly summaries run on Thursday instead of Friday, update `weekly/week-summary.md` and the orchestrator.

### HTML output customisation

Edit `skills/system/routine-html-template.md` to change the visual style (colours, fonts, layout). All routine skills reference this template.

### Creating your own utility skill

Follow the same SKILL.md pattern: YAML frontmatter with `name` and `description`, then sections for When to Use, Tools, Workflow, Output, and Guidelines. The description field controls when Cursor suggests the skill.

## Design Principles

- **Skill-per-step:** every routine step is a standalone skill that knows exactly what to do. Skills can be improved independently and reused outside the routine system.
- **State as YAML:** simple, human-readable, easy to debug. No database needed.
- **Idempotent:** if a session crashes mid-routine, the next session picks up where it left off (incomplete steps stay `false`).
- **Progressive:** placeholder skills let you define the full routine now and activate steps as you connect new tools.
- **Visual output:** HTML reports with dark-themed styling provide a scannable, dashboard-like view of your morning briefing and weekly reviews.
- **Configurable:** the onboarding flow and config file make it straightforward to adapt the system to any product, team, or workflow.

## Requirements

- [Cursor IDE](https://cursor.com/) with agent mode
- Slack MCP (for most skills)
- Kremer MCP or equivalent data warehouse MCP (for metrics)
- monday API MCP (for meeting review)
- Additional MCPs as needed (see [MCP Requirements](#connect-required-mcps))

## Licence

MIT
