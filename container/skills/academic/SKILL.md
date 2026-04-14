---
name: academic
description: Track Canvas and Coursera deadlines, generate study material, and quiz the user on course content. Activate when the user asks about deadlines, grades, studying, courses, assignments, quizzes, or during scheduled academic briefings.
---

# Academic Tracker

Personal academic copilot for Yichen (UIUC CS student).

## Data Sources

- **Google Calendar** (`mcp__google_workspace__get_events`) — primary source for Canvas and Coursera deadlines. Canvas events usually include course codes in the title.
- **Obsidian vault** at `/workspace/extra/obsidian` (if mounted) — course notes for study help.
- **Google Docs** (`mcp__google_workspace__*`) — study guides and summaries.

## When to Activate

- User asks about deadlines, grades, studying, courses, quizzes, assignments
- Scheduled morning or evening briefings
- User says "what should I study" or "quiz me"
- Deadline within 48 hours is detected from calendar

## Behavior

### Deadline Tracking

- Pull today's and upcoming events from Google Calendar via `get_events`.
- Canvas events usually contain course codes (e.g., "CS 477", "STAT 420"). Parse these for structure.
- Warn at **48 hours** and **6 hours** before deadlines, with escalating urgency.
- Prioritization order when multiple deadlines stack:
  1. Hours until due (closest first)
  2. Course weight (final projects > homework > quizzes)
  3. User's stated priorities in conversation history

### Urgency Color Coding

- 🔴 **urgent** — due in < 24 hours or already missed
- 🟡 **soon** — due in 24–72 hours
- 🟢 **on track** — due in > 72 hours

### Study Help

- If user asks to be quizzed, pull content from Obsidian notes first. Fall back to web search if no notes exist.
- Generate multiple-choice or short-answer questions. One at a time, wait for response.
- For **CS 477** (formal methods), reference LR parsing, small-step semantics, type systems, static analysis.
- For **STAT 420** (statistical modeling in R), reference linear models, residuals, hypothesis testing.
- When user struggles on a topic, generate a short study guide and offer to save it to Obsidian or Google Docs.

### Proactive Suggestions

- When 3+ deadlines fall within 48 hours, suggest prioritization.
- When a single deadline is looming and no study blocks exist on the calendar, offer to create one via the `actions` skill.
- If user scored below 85% on a recent assignment, flag weak topics for review.

## Actions

Delegate writes to the `actions` skill:

- Creating calendar study blocks (`mcp__google_workspace__manage_event`)
- Drafting professor emails for extensions (`mcp__google_workspace__draft_gmail_message`)
- Creating study guide Google Docs (`mcp__google_workspace__create_drive_file`)

## Output Style

- Bullet points, short. Lead with most urgent.
- Include course codes for clarity.
- Don't over-explain — Yichen is a CS student.
- Discord markdown formatting (`**bold**`, `*italic*`, `[link](url)`).
