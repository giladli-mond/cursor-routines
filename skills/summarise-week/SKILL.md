---
name: summarise-week
description: Consolidates the week's activity, metrics, Slack discussions, and meetings into a single weekly summary. Use as part of the weekly Friday routine.
---

# Summarise Week

## When to Use

This skill is called by the weekly routine on **Friday**. It can also be run independently when asked for a week-in-review.

## Inputs

- Daily metrics outputs from the current week (in conversation history or `your output folder/`)
- Daily Slack summaries from the current week
- Meeting digests from the notetaker
- Gmail highlights from the current week (important emails surfaced in daily routines)
- Calendar events from the current week (via Google Workspace MCP)
- Product Ops weekly summary (if already generated for this week in `your output folder/`)
- Previous week summaries in `your output folder/` for trend comparison

## Slack Channel IDs (for message sending)

| Channel | ID | Audience |
|---------|-----|----------|
| `#monday-campaigns` | `C08H4KT85DZ` | Internal team |
| `#campaigns-announcements` | `C08S3K72LG5` | Company-wide |
| `#campaigns-board` | `C0A77SGN8UU` | Directors (eng, product, GTM, design) |

## Tools

### Slack MCP (`user-slack`)

| Tool | Purpose |
|------|---------|
| `slack_search_channels` | Find campaigns channels |
| `slack_search_public` | Search for key discussions from this week |
| `slack_send_message_draft` | Create draft messages in Slack (param: `message`, not `text`) |

### Google Workspace MCP (`user-google-workspace`)

| Tool | Purpose |
|------|---------|
| `gmail_search` | Search for important emails from the week |
| `gmail_get` | Get full content of specific emails for context |
| `calendar_listEvents` | List this week's calendar events for meeting summary |

### Kremer MCP (`user-kremer-mcp`)

| Tool | Purpose |
|------|---------|
| `execute-sql-query` | Query weekly metrics aggregates |
| `data-expert-agent` | Explore trends across the week |

### Monday MCP (`user-monday-api-mcp`)

| Tool | Purpose |
|------|---------|
| `get_notetaker_meetings` | Retrieve this week's meetings for summary |

## Workflow

### Step 1 -- Gather inputs

1. Read `your data guide` for current metric definitions
2. Check `your output folder/` for any Product Ops summaries from this week
3. Check `your output folder/` for last week's summary (for continuity)
4. Retrieve this week's meetings via the notetaker

### Step 2 -- Compile metrics

Query weekly aggregates for the key metrics (use the same metrics as `review-daily-metrics` but aggregated to weekly level):
- Weekly averages vs previous week
- Trend direction for each metric
- Any standout days (spikes, drops)

### Step 3 -- Summarise Slack activity

Search for the most discussed topics across campaigns channels this week. Identify:
- Major decisions made
- Issues raised and resolved (or still open)
- Cross-team discussions
- Customer-related threads

### Step 4 -- Summarise meetings

From the notetaker data:
- Key decisions from each meeting
- Action items (grouped by owner)
- Recurring themes across meetings

Also pull this week's calendar events for a complete meeting picture:

```json
{
  "server": "user-google-workspace",
  "toolName": "calendar_listEvents",
  "arguments": {
    "calendarId": "primary",
    "timeMin": "YYYY-MM-DDT00:00:00Z",
    "timeMax": "YYYY-MM-DDT23:59:59Z"
  }
}
```

Use this week's Monday as `timeMin` and Friday as `timeMax`. Cross-reference with notetaker data to fill gaps. Note:
- Total meetings this week and hours spent in meetings
- Customer/external meetings held (flag these separately)
- Any meetings that were cancelled or rescheduled

### Step 4b -- Summarise email highlights

Search Gmail for important emails from this week:

```json
{
  "server": "user-google-workspace",
  "toolName": "gmail_search",
  "arguments": {
    "query": "newer_than:7d -category:promotions -category:social -category:forums",
    "maxResults": 50,
    "labelIds": ["INBOX"]
  }
}
```

From the results, identify the most significant emails of the week:
- Emails from stakeholders, leadership, or external partners
- Emails requiring action that may still be outstanding
- Emails tied to key decisions or product changes

For each, include a direct link: `https://mail.google.com/mail/u/0/#inbox/<messageId>`

### Step 5 -- Write the summary

Produce a cohesive weekly summary that connects the threads.

### Step 6 -- Draft Slack messages

After completing the summary, draft three Slack messages. Each has a different audience and tone. Read `your tone of voice guide` before writing and apply `make-human-lite` to each message.

**Before drafting:** read `your output folder/message-history.md` to review all past messages. This gives you context on what's been said before, what tone landed well, and avoids repetition. If the file doesn't exist, create it.

#### Message 1: Team update (`#monday-campaigns`)

**Audience:** The monday campaigns team (designers, engineers, GTM, analyst).
**Tone:** No fluff. Festive (it's Friday). True, honest, humble. These are the people who did the work, so give credit where it's due. Name people. Be specific about what shipped and what's next. If something didn't go well, say so honestly. Don't oversell, don't pad.
**Length:** Short. 8-15 lines. Bullet points for what shipped, a couple of numbers, a shoutout, done.
**Format:** Slack-native formatting (`*bold*`, `•` bullets).

#### Message 2: Company announcement (`#campaigns-announcements`)

**Audience:** The broader monday.com company.
**Tone:** Positive, festive, informative. This is a "look what the campaigns team shipped" moment. People outside the team should understand what happened and why it matters. Be proud but not boastful. Focus on customer impact and product progress. Make it easy to skim.
**Length:** Medium. 10-20 lines. A headline, key highlights, a metric or two, and what's coming next.
**Format:** Slack-native formatting. Use emoji sparingly (one or two, not a wall of them).

#### Message 3: Director summary (`#campaigns-board`)

**Audience:** Campaigns directors: Director of Engineering, Director of Product, Director of GTM, Director of Design.
**Tone:** High-level and to the point. These are senior leaders who want the signal, not the noise. Lead with what matters: what shipped, where the metrics are, what's ahead. No filler. Numbers first.
**Length:** Short. 6-12 lines. A one-line summary, key metrics, top 2-3 things that happened, one thing to watch.
**Format:** Slack-native formatting.

### Step 7 -- Record messages

After approval of and sends each message, append all three to `your output folder/message-history.md` in this format:

```markdown
---

## Week of [date range]

### Team (#monday-campaigns)
[Full message text as sent]

### Company (#campaigns-announcements)
[Full message text as sent]

### Directors (#campaigns-board)
[Full message text as sent]
```

This creates a running record of all weekly communications. The agent should read this file before drafting new messages to maintain continuity and avoid repeating the same phrasing week after week.

## Output

Generate an HTML file using the routine template at `skills/system/routine-html-template.md`. Read that file for the full CSS and component patterns.

**File:** Save to `your output folder/week-summary-YYYY-MM-DD.html`
**Also save** a markdown copy to `your output folder/week-summary-YYYY-MM-DD.md` for archival and cross-referencing by other skills.
**Open:** Run `open` on the HTML file so it launches in the browser.
**Inline:** Also print a 3-4 line summary in the conversation.

### HTML structure for this skill

```html
<div class="header">
  <h1>Week in Review</h1>
  <div class="subtitle">Week of [date range] · [N] meetings · [N] decisions</div>
</div>

<div class="section">
  <div class="summary-text">[3-4 sentences capturing the most important things. What moved, what matters, what to carry forward.]</div>
</div>

<hr class="divider">

<div class="section">
  <div class="section-title">What Shipped</div>
  <div class="card success">
    <div class="card-title">[Item]</div>
    <div class="card-body">[Brief detail]</div>
  </div>
  <!-- repeat -->
</div>

<div class="section">
  <div class="section-title">Metrics Snapshot</div>
  <div class="metric-grid">
    <div class="metric-card">
      <div class="metric-value">[N]</div>
      <div class="metric-label">DAU (avg)</div>
      <div class="metric-change [up/down/flat]">[+/- %] vs last week</div>
    </div>
    <!-- repeat for each metric -->
  </div>

  <table>
    <thead>
      <tr><th>Metric</th><th>This week</th><th>Last week</th><th>Change</th></tr>
    </thead>
    <tbody>
      <tr>
        <td>[Metric]</td><td>[Value]</td><td>[Value]</td>
        <td><span class="badge [green/red/gray]">[+/- %]</span></td>
      </tr>
    </tbody>
  </table>
</div>

<div class="section">
  <div class="section-title">Key Decisions</div>
  <div class="card highlight">
    <div class="card-title">[Decision]</div>
    <div class="card-body">[Context, who decided]</div>
    <div class="card-meta"><span class="channel">#[channel]</span> or [meeting name]</div>
  </div>
</div>

<div class="section">
  <div class="section-title">Carry to Next Week</div>
  <div class="task-row">
    <div class="task-content">[Item with status and owner]</div>
    <button class="btn-todoist" onclick="this.classList.toggle('added'); this.innerHTML = this.classList.contains('added') ? '<span class=\'icon\'>✓</span> Added' : '<span class=\'icon\'>+</span> Add to Todoist'">
      <span class="icon">+</span> Add to Todoist
    </button>
  </div>
</div>

<div class="section">
  <div class="section-title">Meetings · [N] this week</div>
  <ul class="item-list">
    <li><strong>[Meeting title]</strong>: [1-line outcome]</li>
  </ul>
</div>

<div class="section">
  <div class="section-title">Email Highlights</div>
  <div class="card highlight">
    <div class="card-title"><a href="https://mail.google.com/mail/u/0/#inbox/[messageId]" target="_blank">[Subject]</a></div>
    <div class="card-body">From [Sender] · [1-line summary or outcome]</div>
    <div class="card-meta">[Day received]</div>
  </div>
  <!-- repeat for each notable email; use warning card for unanswered, highlight for informational -->
</div>

<div class="section">
  <div class="section-title">Worth Watching</div>
  <div class="card warning">
    <div class="card-title">[Trend or signal]</div>
    <div class="card-body">[Why it matters]</div>
  </div>
</div>

<hr class="divider">

<div class="send-instructions">
  <strong>How sending works:</strong> These buttons mark messages as approved. To actually send, go back to Cursor and tell the agent which messages to post. The agent will use Slack to send them on your behalf.
</div>

<!-- MESSAGE 1: Team -->
<div class="section">
  <div class="message-header">
    <div class="section-title">Team Update</div>
    <div class="message-channel">Will post to <span class="channel">#monday-campaigns</span></div>
  </div>
  <div class="message-preview">[Draft team message here, using Slack formatting: *bold*, • bullets]</div>
  <button class="btn-slack" onclick="this.classList.toggle('sent'); this.innerHTML = this.classList.contains('sent') ? '<span class=\'slack-icon\'>✓</span> Approved' : '<span class=\'slack-icon\'>☐</span> Approve to send'">
    <span class="slack-icon">☐</span> Approve to send
  </button>
</div>

<!-- MESSAGE 2: Company -->
<div class="section">
  <div class="message-header">
    <div class="section-title">Company Announcement</div>
    <div class="message-channel">Will post to <span class="channel">#campaigns-announcements</span></div>
  </div>
  <div class="message-preview">[Draft company message here]</div>
  <button class="btn-slack" onclick="this.classList.toggle('sent'); this.innerHTML = this.classList.contains('sent') ? '<span class=\'slack-icon\'>✓</span> Approved' : '<span class=\'slack-icon\'>☐</span> Approve to send'">
    <span class="slack-icon">☐</span> Approve to send
  </button>
</div>

<!-- MESSAGE 3: Directors -->
<div class="section">
  <div class="message-header">
    <div class="section-title">Director Summary</div>
    <div class="message-channel">Will post to <span class="channel">#campaigns-board</span></div>
  </div>
  <div class="message-preview">[Draft director message here]</div>
  <button class="btn-slack" onclick="this.classList.toggle('sent'); this.innerHTML = this.classList.contains('sent') ? '<span class=\'slack-icon\'>✓</span> Approved' : '<span class=\'slack-icon\'>☐</span> Approve to send'">
    <span class="slack-icon">☐</span> Approve to send
  </button>
</div>
```

After the HTML is presented, ask the user in the conversation which messages to send. When he confirms, use `slack_send_message_draft` for each approved message (never `slack_send_message` directly). Then record all messages in `your output folder/message-history.md`.

## Guidelines

- This should be a synthesis, not a concatenation of daily summaries
- Lead with what matters most. The opening paragraph is the most important section
- Compare with last week where possible to show trajectory
- If the Product Ops weekly summary exists, build on it rather than duplicating
- Create the `your output folder/` directory if it doesn't exist

## Change Log

| Date | Change |
|------|--------|
| 20 Mar 2026 | Added Google Calendar as input source (Step 4), calendar event listing for complete meeting picture, customer meeting flagging |
| 19 Mar 2026 | Added Gmail as input source (Step 4b), Email Highlights section in HTML output, Google Workspace MCP tools |
| 13 Mar 2026 | Initial version |
| 13 Mar 2026 | Added HTML output. Added three Slack messages: team (#monday-campaigns), company (#campaigns-announcements), directors (#campaigns-board). Added message history archival to your output folder/message-history.md |
