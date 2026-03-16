---
name: summarise-slack-and-suggest-tasks
description: Consolidates Slack findings from DMs and Campaigns channels reviews, produces a unified morning summary, and suggests actionable Todoist tasks. Use as part of the daily start-of-day routine.
---

# Summarise Slack + Suggest Tasks

## When to Use

This skill is called by the start-of-day routine (step 3), **after** `review-slack-dms` and `review-campaigns-channels` have completed. It synthesises their output and produces actionable task suggestions.

## Inputs

- Output from `review-slack-dms` (step 1 of start-of-day), including threads the user is part of with new replies
- Output from `review-campaigns-channels` (step 2 of start-of-day), including campaigns channel threads the user is part of with new replies

Both should be available in the current conversation context from the preceding steps.

## Tools

### Todoist (via skill)

| Skill | Purpose |
|-------|---------|
| `add-task-to-todoist` (`~/.cursor/skills/add-task-to-todoist/SKILL.md`) | Create suggested tasks in Todoist |

Read the Todoist skill before creating any tasks. Follow its conventions (Work project, To Do section, actionable titles ending with robot emoji).

**Note:** If the Todoist MCP is errored, present the suggested tasks as text instead and let the user add them manually.

## Workflow

### Step 1 -- Consolidate findings

Review the output from the previous two skills. Identify:
- **Items needing the user's response** (DMs awaiting reply, questions in channels, thread replies waiting for him)
- **Items needing the user's action** (decisions to make, documents to review, approvals)
- **Items worth tracking** (emerging topics, things to follow up on later)
- **Threads awaiting quick reply** (threads the user is part of where someone has replied since his last message)
- **Patterns across channels** (same topic appearing in multiple places, escalating issues)

### Step 2 -- Write the morning Slack summary

Produce a single consolidated summary. This is the "morning briefing" for the user.

### Step 3 -- Suggest Todoist tasks

Based on the consolidated findings, draft 3-7 suggested tasks. Each task should be:
- **Actionable:** starts with a verb ("Reply to", "Review", "Follow up on")
- **Specific:** references the person, channel, or topic
- **Proportionate:** don't create tasks for trivial FYI items

Present the suggested tasks for approval before creating them in Todoist.

### Step 4 -- Create approved tasks

After confirmation of which tasks to create, use the `add-task-to-todoist` skill to create them. If the user modifies a task title, use his version.

## Output

This skill produces the **combined morning briefing HTML file** that includes:
1. The DM summary section (from `review-slack-dms`)
2. The campaigns channels section (from `review-campaigns-channels`)
3. The consolidated top priorities
4. Suggested Todoist tasks with "Add to Todoist" buttons

Generate the HTML using the routine template at `skills/system/routine-html-template.md`. Read that file for the full CSS and component patterns.

**File:** Save to `your output folder/morning-briefing-YYYY-MM-DD.html`
**Open:** Run `open` on the file so it launches in the browser.
**Inline:** Also print a 2-3 line summary in the conversation.

### HTML structure for this skill

The file wraps all morning content in the base HTML template (copy the full `<style>` block from the template file). The body contains the DM and channel sections from the previous skills, followed by this skill's own sections:

```html
<div class="header">
  <h1>Morning Briefing</h1>
  <div class="subtitle">[Day, Date] · [N] DMs · [N] channels · [N] messages</div>
</div>

<!-- DM section from review-slack-dms (insert here) -->
<!-- Channels section from review-campaigns-channels (insert here) -->

<hr class="divider">

<div class="section">
  <div class="section-title">Top Priorities</div>
  <ol class="priority-list">
    <li>
      <div class="card-title">[Most important thing]</div>
      <div class="card-body">[Context from Slack]</div>
    </li>
    <li>
      <div class="card-title">[Second]</div>
      <div class="card-body">[Context]</div>
    </li>
  </ol>
</div>

<div class="section">
  <div class="section-title">Suggested Tasks</div>
  <div class="task-row">
    <div class="task-content">Reply to [person] about [topic]</div>
    <button class="btn-todoist" onclick="this.classList.toggle('added'); this.innerHTML = this.classList.contains('added') ? '<span class=\'icon\'>✓</span> Added' : '<span class=\'icon\'>+</span> Add to Todoist'">
      <span class="icon">+</span> Add to Todoist
    </button>
  </div>
  <!-- repeat for each suggested task -->
</div>
```

After presenting the HTML, ask the user in the conversation which tasks (if any) to actually create in Todoist. The buttons are visual indicators; the agent handles actual creation via the Todoist MCP.

## Guidelines

- The summary should take 30 seconds to read, not 5 minutes
- Suggest tasks only for things that genuinely need doing
- Do not duplicate items already visible in the DM and channel summaries, add the synthesis layer
- If there's genuinely nothing important, say so. Don't invent urgency
- Wait for the user's confirmation before creating Todoist tasks

## Change Log

| Date | Change |
|------|--------|
| 16 Mar 2026 | Updated inputs and consolidation to include thread participation data from upstream skills |
| 13 Mar 2026 | Initial version |
