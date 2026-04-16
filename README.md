# Oh My Bruno

AI personal assistant system powered by Claude Code Remote Triggers.

Automatically generates daily briefings, evening reviews, and weekly summaries by collecting data from Google Calendar, Linear, Slack, Notion, and Git.

## Structure

```
config.md              # User-specific configuration (Notion DB IDs, Slack ID, etc.)
prompts/               # Execution instructions for each scheduled task
  morning-briefing.md  # Morning briefing prompt
  evening-review.md    # Evening review prompt (with feedback integration)
  weekly-summary.md    # Weekly summary prompt
templates/             # Notion page content templates
  daily-page.md        # Daily page structure
  weekly-page.md       # Weekly page structure
setup-guide.md         # Remote trigger IDs and setup reference
```

## Setup

1. Fork this repo
2. Edit `config.md` with your Notion DB IDs, Slack user ID, etc.
3. Connect MCP connectors at https://claude.ai/settings/connectors (Notion, Calendar, Slack, Linear)
4. Create Remote Triggers at https://claude.ai/code/scheduled pointing to your fork
5. Attach MCP connectors to each trigger

## Daily Flow

1. **Morning** (~8am): Collects calendar events, Linear issues, Slack mentions, Git status -> Creates Notion Daily page
2. **User**: Reviews briefing throughout the day, writes feedback in the "Today's Feedback" section
3. **Evening** (~7pm): Reads user feedback, collects day's progress -> Appends evening review to the same page
4. **Friday** (~6pm): Aggregates weekly data -> Creates weekly summary page
