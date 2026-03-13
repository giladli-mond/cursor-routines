---
name: summarise-campaigns-day
description: Reviews product-related Slack messages from today, produces a summary with highlights, key decisions, and follow-up items. Use as part of the daily end-of-day routine.
---

# Summarise Campaigns Day

## When to Use

This skill is called by the end-of-day routine (step 1). It can also be run independently when asked to summarise today's product activity.

## Configuration

Set `CHANNEL_KEYWORD` to match your product channels (same as `review-campaigns-channels`).

## Inputs

- Time window: today (from midnight or start of working day)
- Channels: all Slack channels matching your product keyword

## Tools

### Slack MCP

| Tool | Purpose |
|------|---------|
| `slack_search_channels` | Find channels matching the keyword |
| `slack_read_channel` | Read today's messages from each channel |
| `slack_read_thread` | Read thread replies for key discussions |
| `slack_search_public` | Search for specific topics or mentions |

## Workflow

### Step 1 -- Find product channels

Search for channels matching your keyword.

### Step 2 -- Read today's messages

For each channel, read messages from today. Paginate if needed.

### Step 3 -- Deep-dive on important threads

For threads with multiple replies, decisions, or escalations, read the full thread.

### Step 4 -- Analyse the day

Across all channels, identify:
- **Decisions made:** What was decided today?
- **Progress updates:** What moved forward?
- **Issues raised:** New bugs, blockers, or concerns?
- **Customer mentions:** Any customer feedback, wins, or complaints?
- **Cross-team coordination:** Threads involving other teams?
- **Unresolved items:** Questions or discussions still open

### Step 5 -- Produce summary

## Output

```
**Day Summary** | [date]

**Key decisions:**
- [Decision] (in #[channel])

**Progress:**
- [What moved forward] -- [brief detail]

**Issues to watch:**
- [Issue or blocker] -- [status and who's on it]

**Open items (carry to tomorrow):**
- [Unresolved question or pending action]
```

If it was a quiet day, say so briefly.

## Guidelines

- This is a retrospective, not a live report. Summarise, don't replay
- Focus on what changed or what matters, not a chronological log
- If a topic from the morning review progressed during the day, note the update
- Keep it under 200 words unless something significant happened

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
