---
name: review-campaigns-channels
description: Finds and reads all Slack channels matching a keyword. Summarises recent activity, flags important threads. Use as part of the daily start-of-day routine.
---

# Review Campaigns Channels

## When to Use

This skill is called by the start-of-day routine (step 2). It can also be run independently when asked to review product Slack activity.

## Configuration

Set `CHANNEL_KEYWORD` to match your product channels. Default: `"campaigns"`. Change this to match your product or team's channel naming convention.

## Inputs

- Channel search keyword (see Configuration above)
- Time window: last 24 hours from now

## Tools

### Slack MCP

| Tool | Purpose |
|------|---------|
| `slack_search_channels` | Find channels matching the keyword |
| `slack_read_channel` | Read recent messages from each channel |
| `slack_read_thread` | Read thread replies for important conversations |

## Workflow

### Step 1 -- Find channels

```
Tool: slack_search_channels
Arguments:
  query: "CHANNEL_KEYWORD"
  channel_types: "public_channel,private_channel"
  include_archived: false
```

Note all channel IDs and names returned.

### Step 2 -- Read recent messages

For each channel found, read messages from the last 24 hours:

```
Tool: slack_read_channel
Arguments:
  channel_id: [channel ID]
  oldest: [Unix timestamp for 24 hours ago]
  limit: 100
```

If a channel has more than 100 messages, paginate using the `cursor` parameter.

### Step 3 -- Read important threads

For messages with thread replies (especially those with multiple responses or reactions), read the full thread.

### Step 4 -- Analyse and summarise

For each channel, identify:
- **Key discussions:** What topics were actively discussed?
- **Decisions made:** Were any decisions reached?
- **Questions asked:** Were there unanswered questions worth knowing about?
- **Escalations:** Anything flagged as urgent or blocked?
- **Notable activity:** Unusual volume, new topics, or cross-team threads

### Step 5 -- Produce summary

Write a summary grouped by channel.

## Output

Present directly in the conversation.

```
**Product Channels** | Last 24 hours | [N] channels checked

**[#channel-name]** ([message count] messages)
- [Key topic or thread] -- [1-2 sentence summary]
- [Another topic] -- [summary]
- Action needed: [if any]

**[#another-channel]** ([message count] messages)
- [Topics and summaries]

**Quiet channels:** [list any channels with no new messages]
```

If no matching channels are found, flag this as unexpected and move on.

## Guidelines

- One to two lines per topic, not full recaps
- Lead with the most active or important channels
- Flag anything that looks like it needs direct input
- Note the channel name and message count so activity levels are clear
- Skip bot-only messages unless they contain actionable content

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
