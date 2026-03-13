---
name: plan-quarter
description: Supports quarterly planning by reviewing current quarter progress, summarising themes, and drafting priorities for the next quarter. Use during the last month of each quarter.
---

# Plan Quarter

## When to Use

This skill is triggered during the **last month of each quarter** as part of the weekly routine. It can also be run on demand for quarterly planning.

This is a thinking aid and planning scaffold, not a fully automated task.

## Inputs

- Current quarter's OKRs (if documented)
- Weekly summaries from across the quarter
- Product strategy documentation
- Competitor analysis reports (if available)
- Scheduled tasks and their completion status

## Tools

### Data Warehouse MCP

| Tool | Purpose |
|------|---------|
| SQL query tool | Query quarterly metrics for progress review |

## Workflow

### Step 1 -- Review current quarter

Read available weekly summaries from this quarter. Identify:
- What the quarter's goals were
- What was actually shipped and accomplished
- Which goals were met, partially met, or missed
- Key decisions made and their outcomes

### Step 2 -- Summarise themes

Across the quarter's work, identify major themes:
- What topics dominated?
- What customer patterns emerged?
- What competitive shifts happened?
- What surprised us?

### Step 3 -- Assess metrics trajectory

Query quarterly metrics and compare against goals.

### Step 4 -- Draft next quarter priorities

Based on the review, suggest:
- 3-5 themes or priorities for next quarter
- For each: why it matters, what success looks like
- Carry-forward items from this quarter
- New opportunities identified

### Step 5 -- Produce the planning document

## Output

Save to your quarterly planning folder and present in conversation.

```
# Quarterly Planning | Q[N] [YYYY] Review + Q[N+1] Priorities

## Quarter in review

**Goals set:** [brief summary]

**What we shipped:**
- [Item] -- [impact if known]

**What we didn't get to:**
- [Item] -- [why, and whether it still matters]

## Metrics trajectory

| Metric | Start of Q | End of Q | Change | Target | Hit? |
|--------|-----------|---------|--------|--------|------|

## Key themes this quarter

1. [Theme] -- [what we learned]

## Suggested priorities for next quarter

1. **[Priority]** -- [Why it matters. What success looks like.]
2. **[Priority]** -- [Why. Success criteria.]
3. **[Priority]** -- [Why. Success criteria.]

## Open questions for planning

- [Question that needs discussion before finalising]
```

## Guidelines

- This is a draft, not a final plan. Present it as "here's a starting point"
- Be honest about what didn't work. Don't spin misses
- Connect priorities to evidence (metrics, customer feedback, competitive moves)
- If data for the quarter is incomplete, say so and work with what's available

## Change Log

| Date | Change |
|------|--------|
| 13 Mar 2026 | Initial version |
