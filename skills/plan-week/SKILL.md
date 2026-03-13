---
name: plan-week
description: Reviews priorities, outstanding tasks, upcoming commitments, and last week's context to help set the weekly focus. Use as part of the weekly Monday routine.
---

# Plan Week

## When to Use

This skill is called by the weekly routine on **Monday**. It can also be run independently when asked to plan the week.

## Inputs

- Last week's summary (for carry-overs)
- Scheduled tasks due this week
- Product strategy context

## Tools

### Slack MCP

| Tool | Purpose |
|------|---------|
| `slack_search_public` | Check for any weekend messages or Monday-morning threads |

## Workflow

### Step 1 -- Review last week

Read the most recent weekly summary. Note:
- Open items carried forward
- Trends worth watching
- Decisions that need follow-up

### Step 2 -- Check scheduled tasks

Read your scheduled tasks file. Identify:
- Tasks due this week (one-off with approaching due dates)
- Recurring tasks that run this week
- Overdue tasks that haven't been completed

### Step 3 -- Scan for Monday context

Check Slack for any weekend activity or early Monday threads.

### Step 4 -- Draft the weekly plan

## Output

```
**Week Plan** | Week of [date]

**Top priorities this week:**
1. [Highest priority] -- [why and what "done" looks like]
2. [Second priority] -- [why and what "done" looks like]
3. [Third priority] -- [why and what "done" looks like]

**Carry-overs from last week:**
- [Item] -- [current status]

**Scheduled tasks due this week:**
- [Task] -- [owner] -- [due: day]

**Key meetings to prepare for:**
- [Meeting] -- [day]

**One thing to watch:**
- [Emerging trend, risk, or opportunity]
```

## Guidelines

- Keep it to 3 top priorities. More than that is no longer prioritised
- "Done" should be concrete, not vague ("Ship the fix" not "Work on the fix")
- If last week's summary isn't available, work with what you have and note the gap
- This is a planning aid, not a command. The user decides the final priorities

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
