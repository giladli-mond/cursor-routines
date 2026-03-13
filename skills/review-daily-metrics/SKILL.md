---
name: review-daily-metrics
description: Queries main product metrics and surfaces highlights, anomalies, and trends. Use as part of the daily start-of-day routine.
---

# Review Daily Metrics

## When to Use

This skill is called by the start-of-day routine (step 4). It can also be run independently for a quick metrics check.

## Configuration

Customise the **Metrics to Track** table below to match your product. The SQL queries depend on your data warehouse schema.

## Inputs

- Your data guide or schema documentation (read before querying)
- Product strategy context (for interpreting metrics)

## Tools

### Data Warehouse MCP

This skill is designed for use with a SQL-capable MCP (e.g. Kremer, Snowflake, BigQuery). Adapt the tool names to your setup.

| Tool | Purpose |
|------|---------|
| SQL query tool | Run SQL against your data warehouse for specific metrics |
| Schema explorer | Explore table schemas and column descriptions |

## Metrics to Track

Customise this table for your product. These are example defaults.

| Metric | What it measures | Why it matters |
|--------|-----------------|----------------|
| DAU | Daily active users | Product health pulse |
| Core actions (last 24h) | Volume of primary product actions | Core usage signal |
| Creation rate | New objects/items created | Engagement signal |
| New signups (last 24h) | New accounts entering the product | Growth indicator |
| Activation rate | Signup to first meaningful action | Onboarding health |
| Conversion to paid | Trial to paid ratio | Revenue pipeline |

## Workflow

### Step 1 -- Read your data guide

Read your schema documentation for current table names, metric definitions, and known data issues.

### Step 2 -- Query metrics

For each metric in the table above, write and execute a SQL query. Compare today's value against:
- Yesterday (daily change)
- Same day last week (weekly trend)
- 7-day rolling average (smoothed trend)

### Step 3 -- Identify highlights

Flag anything notable:
- **Spikes or drops** > 15% compared to the 7-day average
- **Trend changes** (e.g. 3+ consecutive days of decline)
- **Records** (highest/lowest in the past 30 days)
- **Anomalies** (unexpected patterns, data gaps)

### Step 4 -- Produce summary

Write a concise metrics summary with highlights.

## Output

Present directly in the conversation.

```
**Daily Metrics** | [date]

| Metric | Today | vs Yesterday | vs 7-day avg | Trend |
|--------|-------|-------------|-------------|-------|
| DAU | [N] | [+/- %] | [+/- %] | [stable/up/down] |
| Core actions | [N] | [+/- %] | [+/- %] | [stable/up/down] |
| New signups | [N] | [+/- %] | [+/- %] | [stable/up/down] |
| Activation rate | [%] | [+/- pp] | [+/- pp] | [stable/up/down] |
| Conversion to paid | [%] | [+/- pp] | [+/- pp] | [stable/up/down] |

**Highlights:**
- [Notable finding, e.g. "DAU up 22% vs last week, driven by..."]
- [Another highlight]

**Worth watching:**
- [Emerging trend or anomaly]
```

If any metric can't be queried (missing table, data gap), note it and move on.

## Guidelines

- Lead with the most important highlight, not the full table
- Be specific with numbers: "up 12%" not "increased"
- Flag data quality issues honestly (small samples, missing days, known pipeline delays)
- Read your data guide every time. Table schemas may have changed
- Keep the summary scannable. The picture should be clear in 15 seconds

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version. Customise the Metrics to Track table for your product. |
