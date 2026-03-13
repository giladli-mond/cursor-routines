---
date: "2026-03-13"
daily_start:
  flag: 0
  review_slack_dms: false
  review_campaigns_channels: false
  summarise_slack_suggest_tasks: false
  review_daily_metrics: false
  # review_upcoming_meetings: false   -- enable when Calendar MCP is connected
  # review_gmail: false               -- enable when Gmail MCP is connected
daily_end:
  flag: 0
  summarise_campaigns_day: false
  review_notetaker_meetings: false
weekly:
  week_of: "2026-03-10"
  flag: 0
  summarise_week: false
  plan_week: false
  plan_quarter: false
---

# Routine State

This file tracks whether daily and weekly routines have been completed. The orchestrator reads and updates it automatically.

**Copy this file to `state.md` before first use.** The `.gitignore` excludes `state.md` to keep your personal execution state private.

## How It Works

- `date`: the date this file was last checked. If it's older than today, all daily flags reset.
- `flag`: group-level completion flag. `1` means all steps in that group are done. `0` means at least one step remains.
- Individual fields (e.g. `review_slack_dms`): per-step completion. `true` when the skill has finished.
- `week_of`: the Monday of the current week. Resets weekly flags when a new week begins.
- Commented-out fields are placeholders for future MCP integrations.

## Customising

Add or remove fields to match your routine steps. Each field should correspond to a skill in the `skills/` folder and a step in the relevant routine file (`daily/start-of-day.md`, etc.).
