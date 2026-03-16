# Cursor Routines

A system for automating daily and weekly routines using Cursor AI agents. Built for product managers and anyone who wants their AI assistant to run structured morning briefings, end-of-day summaries, weekly reviews, and quarterly planning.

> **Internal version:** This repo includes monday.com tool references (Kremer MCP, monday AI notetaker, Slack channel conventions). For the fully genericised public version, see [cursor-routines on GitHub](https://github.com/gililivnat/cursor-routines).

## What This Does

Every time you start a Cursor conversation, the system checks a state file to see which routines are due. It runs each step as a dedicated **skill** (a SKILL.md file the agent reads and follows), tracks progress, and avoids repeating work already done today.

The core idea is simple:
- A **flag-based state machine** tracks what's been done today
- Each routine step is a **standalone skill** that can be run independently or improved over time
- The agent resets the flags each morning and works through uncompleted steps

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

The `state.md` file has YAML frontmatter tracking:

```yaml
date: "2026-03-13"
daily_start:
  flag: 0                     # 0 = not done, 1 = all steps complete
  review_slack_dms: false      # individual step tracking
  review_campaigns_channels: false
  ...
```

**Logic:**
1. If `date` < today → reset all daily flags to `false`, set `date` to today
2. If `date` == today and `flag` == 1 → skip (already done)
3. If `date` == today and `flag` == 0 → run steps still marked `false`
4. After each step completes → mark it `true`
5. When all steps are `true` → set the group `flag` to `1`

## Routines Included

### Daily: Start of Day
1. **Review Slack DMs** — scan personal messages and threads you're part of, flag what needs a response
2. **Review product channels** — read all Slack channels matching a keyword, check threads you're in, summarise activity
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
- **HTML output:** Morning briefings, EOD summaries, weekly reports, and plans are generated as styled HTML files with dark theme, metric cards, priority lists, and interactive Todoist/Slack buttons
- **Slack message drafting:** Weekly summaries draft three separate Slack messages (team, company, directors) with appropriate tone and length for each audience
- **Message history:** Weekly Slack messages are archived for cross-week reference, ensuring continuity and avoiding repetition

## Structure

```
cursor-routines/
├── README.md
├── cursor-rule-example.md         — example Cursor rule to activate the system
├── routines/
│   ├── ORCHESTRATOR.md            — flag logic and execution rules
│   ├── state.example.md           — template state file (copy to state.md)
│   ├── daily/
│   │   ├── start-of-day.md
│   │   └── end-of-day.md
│   └── weekly/
│       ├── week-summary.md
│       ├── week-planning.md
│       └── quarterly-planning.md
└── skills/
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
    └── system/
        └── routine-html-template.md      — shared CSS/HTML template for all outputs
```

## Setup

### 1. Copy the files into your workspace

Place the `routines/` and `skills/` folders wherever makes sense in your project structure. Update the file paths in the orchestrator and routine files accordingly.

### 2. Create your state file

```bash
cp routines/state.example.md routines/state.md
```

Edit the date and fields to match today and your active skills.

### 3. Install the Cursor rule

Copy `cursor-rule-example.md` to `.cursor/rules/routines.mdc` (or whatever naming convention you use). This tells Cursor to check routines at the start of every conversation.

### 4. Install skills to Cursor

For Cursor to auto-discover skills, copy each SKILL.md to `~/.cursor/skills/[skill-name]/SKILL.md`:

```bash
for skill in skills/*/; do
  name=$(basename "$skill")
  mkdir -p ~/.cursor/skills/$name
  cp "$skill/SKILL.md" ~/.cursor/skills/$name/SKILL.md
done
```

### 5. Configure for your product

- In `review-campaigns-channels` and `summarise-campaigns-day`: change the channel search keyword to match your product's Slack channels
- In `review-slack-dms`: set `YOUR_SLACK_USER_ID` to your actual Slack user ID
- In `review-daily-metrics`: customise the Metrics to Track table for your product
- In `review-notetaker-meetings`: confirm tool names match your monday AI notetaker MCP setup
- In `summarise-week`: update the Slack Channel IDs table with your team's channels

### 6. Connect required MCPs

The active skills use these monday.com Cursor MCP connections:
- **Slack MCP** (`user-slack`) — for DM and channel review skills
- **Kremer MCP** (`user-kremer-mcp`) — for daily and weekly metrics via Snowflake
- **Monday API MCP** (`user-monday-api-mcp`) — for AI notetaker meeting review
- **Todoist MCP** (optional) — for task suggestion and creation

Optional (currently placeholder):
- **Google Calendar MCP** — for upcoming meetings skill
- **Gmail MCP** — for email review skill

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

Edit the routine files and the orchestrator's "Determine what to run" logic.

### HTML output customisation

Edit `skills/system/routine-html-template.md` to change the visual style (colours, fonts, layout). All routine skills reference this template.

## Design Principles

- **Skill-per-step:** every routine step is a standalone skill that knows exactly what to do. Skills can be improved independently and reused outside the routine system.
- **State as YAML:** simple, human-readable, easy to debug. No database needed.
- **Idempotent:** if a session crashes mid-routine, the next session picks up where it left off (incomplete steps stay `false`).
- **Progressive:** placeholder skills let you define the full routine now and activate steps as you connect new tools.
- **Visual output:** HTML reports with dark-themed styling provide a scannable, dashboard-like view of your morning briefing and weekly reviews.

## Requirements

- [Cursor IDE](https://cursor.com/) with agent mode
- Slack MCP (for most skills)
- Kremer MCP or equivalent data warehouse MCP (for metrics)
- monday API MCP (for meeting review)

## Licence

MIT
