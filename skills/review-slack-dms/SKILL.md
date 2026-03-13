---
name: review-slack-dms
description: Reviews personal Slack direct messages from the last 24 hours. Flags important messages and summarises key conversations. Use as part of the daily start-of-day routine.
---

# Review Slack DMs

## When to Use

This skill is called by the start-of-day routine (step 1). It can also be run independently when asked to check Slack DMs.

## Inputs

- Your Slack user ID (set `YOUR_SLACK_USER_ID` below)
- Time window: last 24 hours from now

## Tools

### Slack MCP

| Tool | Purpose |
|------|---------|
| `slack_read_channel` | Read DM history by passing a user_id as `channel_id` |
| `slack_search_public` | Search for messages sent to you (`to:<@YOUR_SLACK_USER_ID>`) |
| `slack_read_thread` | Read thread replies for deeper context on important messages |
| `slack_search_users` | Look up user details when needed |

## Workflow

### Step 1 -- Find recent DMs

Search for messages directed to you in the last 24 hours:

```
Tool: slack_search_public
Arguments:
  query: "to:<@YOUR_SLACK_USER_ID>"
  sort: "timestamp"
  sort_dir: "desc"
  after: [Unix timestamp for 24 hours ago]
```

Also search for messages from you to capture full conversations:

```
Tool: slack_search_public
Arguments:
  query: "from:<@YOUR_SLACK_USER_ID>"
  sort: "timestamp"
  sort_dir: "desc"
  after: [Unix timestamp for 24 hours ago]
```

### Step 2 -- Read important threads

For messages with thread replies, read the full thread for context:

```
Tool: slack_read_thread
Arguments:
  channel_id: [channel from message]
  message_ts: [thread timestamp]
```

### Step 3 -- Categorise and prioritise

For each conversation, assess:
- **Urgency:** Does this need a reply today?
- **Topic:** What is this about? (product, team, ops, social)
- **Action needed:** Am I expected to do something?

### Step 4 -- Produce summary

Write a concise summary grouped by urgency.

## Output

Present directly in the conversation. Do not send via Slack.

```
**Slack DMs** | Last 24 hours

**Needs response:**
- [Person]: [Topic] -- [1-line summary of what they need]
- ...

**FYI (no action needed):**
- [Person]: [Topic] -- [1-line summary]
- ...

**Threads to watch:**
- [Brief description of active thread worth monitoring]
```

If there are no messages, say so briefly and move on.

## Guidelines

- Be concise: one line per conversation, not paragraphs
- Prioritise by urgency, not chronology
- If a message looks like it needs input, flag it clearly
- Do not read messages older than 24 hours unless a thread started within the window

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
