---
name: summarise-slack-and-suggest-tasks
description: Consolidates Slack findings from DMs and channel reviews, produces a unified morning summary, and suggests actionable tasks. Use as part of the daily start-of-day routine.
---

# Summarise Slack + Suggest Tasks

## When to Use

This skill is called by the start-of-day routine (step 3), **after** the DM review and channel review skills have completed. It synthesises their output and produces actionable task suggestions.

## Inputs

- Output from the DM review skill (step 1 of start-of-day)
- Output from the channel review skill (step 2 of start-of-day)

Both should be available in the current conversation context from the preceding steps.

## Tools

### Task Manager

This skill works with any task management integration (Todoist, Linear, etc.). Adapt the task creation step to your tool of choice.

## Workflow

### Step 1 -- Consolidate findings

Review the output from the previous two skills. Identify:
- **Items needing response** (DMs awaiting reply, questions in channels)
- **Items needing action** (decisions to make, documents to review, approvals)
- **Items worth tracking** (emerging topics, things to follow up on later)
- **Patterns across channels** (same topic appearing in multiple places, escalating issues)

### Step 2 -- Write the morning summary

Produce a single consolidated summary. This is the "morning briefing."

### Step 3 -- Suggest tasks

Based on the consolidated findings, draft 3-7 suggested tasks. Each task should be:
- **Actionable:** starts with a verb ("Reply to", "Review", "Follow up on")
- **Specific:** references the person, channel, or topic
- **Proportionate:** don't create tasks for trivial FYI items

Present the suggested tasks for approval before creating them.

### Step 4 -- Create approved tasks

After confirmation of which tasks to create, use your task management tool to create them.

## Output

```
**Morning Slack Summary**

**Top priorities:**
1. [Most important thing from Slack, with context]
2. [Second most important]
3. [Third]

**Other highlights:**
- [Notable item]
- [Notable item]

**Suggested tasks:**
1. Reply to [person] about [topic] in DM
2. Review [thread/doc] in #[channel]
3. Follow up on [topic] with [person/team]
4. ...

Create these as tasks? (You can edit or skip any.)
```

## Guidelines

- The summary should take 30 seconds to read, not 5 minutes
- Suggest tasks only for things that genuinely need doing
- Do not duplicate items already visible in the DM and channel summaries; add the synthesis layer
- If there's genuinely nothing important, say so. Don't invent urgency
- Wait for confirmation before creating tasks

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
