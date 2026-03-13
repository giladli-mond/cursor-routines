---
name: summarise-week
description: Consolidates the week's activity, metrics, Slack discussions, and meetings into a single weekly summary. Use as part of the weekly Friday routine.
---

# Summarise Week

## When to Use

This skill is called by the weekly routine on **Friday**. It can also be run independently for a week-in-review.

## Inputs

- Daily metrics outputs from the current week
- Daily Slack summaries from the current week
- Meeting digests from the notetaker
- Previous week summaries for trend comparison

## Tools

### Slack MCP

| Tool | Purpose |
|------|---------|
| `slack_search_channels` | Find product channels |
| `slack_search_public` | Search for key discussions from this week |

### Data Warehouse MCP

| Tool | Purpose |
|------|---------|
| SQL query tool | Query weekly metrics aggregates |

### Meeting Notetaker MCP

| Tool | Purpose |
|------|---------|
| Meeting retrieval tool | Retrieve this week's meetings |

## Workflow

### Step 1 -- Gather inputs

1. Read your data guide for current metric definitions
2. Check for any existing ops summaries from this week
3. Check for last week's summary (for continuity)
4. Retrieve this week's meetings via the notetaker

### Step 2 -- Compile metrics

Query weekly aggregates for key metrics:
- Weekly averages vs previous week
- Trend direction for each metric
- Any standout days (spikes, drops)

### Step 3 -- Summarise Slack activity

Search for the most discussed topics across product channels this week. Identify major decisions, issues, cross-team discussions, and customer threads.

### Step 4 -- Summarise meetings

From the notetaker data: key decisions, action items grouped by owner, recurring themes.

### Step 5 -- Write the summary

Produce a cohesive weekly summary that connects the threads.

## Output

Save to your weekly summaries folder and present in conversation.

```
# Week Summary | [date range]

## The week in one paragraph

[3-4 sentences capturing the most important things. What moved, what matters, what to carry forward.]

## What shipped / progressed

- [Item] -- [brief detail]

## Metrics snapshot

| Metric | This week | Last week | Change |
|--------|-----------|-----------|--------|
| DAU (avg) | [N] | [N] | [+/- %] |
| Core actions | [N] | [N] | [+/- %] |

## Key decisions

- [Decision] -- [context, who decided]

## Open items (carry to next week)

- [Item] -- [status and owner]

## Meetings held

[N] meetings this week. Key outcomes:
- [Meeting]: [1-line outcome]

## Worth watching

- [Trend or signal to monitor]
```

## Guidelines

- This should be a synthesis, not a concatenation of daily summaries
- Lead with what matters most. The opening paragraph is the most important section
- Compare with last week where possible to show trajectory
- If an ops weekly summary exists, build on it rather than duplicating

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
