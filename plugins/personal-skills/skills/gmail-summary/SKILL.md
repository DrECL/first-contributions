---
name: gmail-summary
description: Analyze recent Gmail messages to identify important emails requiring responses, filter out marketing and automated notifications, and generate professional draft replies. Use when the user says "gmail-summary" optionally followed by a timeframe (e.g., "gmail-summary this week", "gmail-summary last 3 days").
---

# Gmail Summary

## Overview

This skill retrieves recent Gmail messages, filters out marketing emails and automated notifications (unless they contain deadlines), identifies emails requiring responses, prioritizes them by importance, and generates professional draft replies.

## Workflow

### Step 1: Parse the Timeframe

Extract the timeframe from the user's request:
- Default: last 24 hours if no timeframe specified
- Parse natural language: "this week", "last 3 days", "yesterday", "last week", etc.
- Convert to Gmail search format using `after:YYYY/MM/DD`

### Step 2: Retrieve Emails

Use `search_gmail_messages` to retrieve up to 10 recent emails from individuals:
- Apply the timeframe filter using `after:` operator
- Exclude common marketing senders and bulk email patterns
- Focus on direct communication from individuals
- Use `read_gmail_thread` to get full context for each message

### Step 3: Filter and Analyze

For each email thread, determine:

**Should it be included?**
- âœ… Include: Emails from individuals requiring a response
- âœ… Include: Automated notifications mentioning deadlines or action items
- âŒ Exclude: Marketing emails from brands/companies
- âŒ Exclude: Newsletters and promotional content
- âŒ Exclude: Social media notifications
- âŒ Exclude: Automated notifications without deadlines (CI/CD, monitoring alerts, etc.)
- âŒ Exclude: Emails already replied to in the thread

**What's the importance level?**
- **High Importance**: Urgent keywords (urgent, asap, deadline, critical), direct questions from leadership/key stakeholders, time-sensitive matters
- **Normal Importance**: Direct questions, meeting requests, collaboration needs, general work discussion
- **Low Importance**: FYI emails, optional updates, low-priority requests

### Step 4: Generate Draft Responses

For Normal and Low importance emails, create professional draft responses:

**Tone Guidelines:**
- Professional and formal
- Concise and to the point
- Address the specific question or request
- No unnecessary pleasantries beyond brief acknowledgment
- Sign off with "- Erick"

**Draft Structure:**
```
[Brief acknowledgment if appropriate]

[Direct response to question/request]

[Next steps if applicable]

- Erick
```

### Step 5: Present Summary

Organize results into a clear summary:

```markdown
# Gmail Summary - [Timeframe]

## High Importance (Respond Immediately)
[List emails with key details: sender, subject, why it's urgent]

## Normal Importance
[For each email:]
**From:** [Sender]
**Subject:** [Subject]
**Summary:** [Brief 1-2 sentence summary]
**Draft Response:**
[Draft reply]

## Low Importance
[For each email:]
**From:** [Sender]
**Subject:** [Subject]
**Summary:** [Brief 1-2 sentence summary]
**Draft Response:**
[Draft reply]
```

## Examples

**User:** "gmail-summary"
â†’ Retrieve emails from last 24 hours, analyze and categorize

**User:** "gmail-summary this week"
â†’ Retrieve emails from the past 7 days, analyze and categorize

**User:** "gmail-summary last 3 days"
â†’ Retrieve emails from the past 3 days, analyze and categorize

## Important Notes

- If fewer than 10 relevant emails are found, process all of them
- For High Importance emails, do NOT generate draft responses - user should craft these personally
- Use judgment to identify truly important emails vs. routine communication
- When in doubt about filtering, err on the side of including the email
- Keep draft responses professional but efficient - avoid over-explaining
