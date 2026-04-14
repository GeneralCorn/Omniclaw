---
name: actions
description: Take concrete actions on the user's behalf — draft emails, create calendar events, make Google Docs, manage Google Tasks, and write to the workspace. Activate when the user requests an action or when another skill needs to execute a write.
---

# Action Agent

Concrete actions across Google Workspace and the local filesystem. This skill handles writes; other skills call into it.

## Available Actions

### Email (Gmail MCP)

- `mcp__google_workspace__draft_gmail_message` — draft email (always draft first)
- `mcp__google_workspace__send_gmail_message` — send email (only after user confirms)

**Rules:**
- **Always draft first, show to user, wait for confirmation before sending.**
- Professor emails: formal tone, include course code, concise, polite.
- Include a clear subject line derived from context.
- Sign with the user's name.

### Calendar (Calendar MCP)

- `mcp__google_workspace__manage_event` — create, update, delete events
- `mcp__google_workspace__get_events` — check for conflicts before creating
- `mcp__google_workspace__query_freebusy` — find available time slots

**Rules:**
- **Always check for conflicts before creating events.**
- Study blocks default to 2 hours, titled `[Course Code] Study Block`.
- Label automated entries with a prefix like `[Andy]` so the user knows what was created automatically.
- Set reasonable reminders (30 min default, 15 min for quick tasks).

### Google Docs / Drive (Drive MCP)

- `mcp__google_workspace__create_drive_file` — create new docs
- `mcp__google_workspace__update_drive_file` — update existing
- `mcp__google_workspace__search_drive_files` — find existing docs

**Naming convention:**
- `YYYY-MM-DD - [Topic]` for dated documents
- `[Course Code] - [Topic] Study Guide` for study guides
- `Weekly Review - Week of YYYY-MM-DD` for weekly reviews

### Google Tasks (Tasks MCP)

- `mcp__google_workspace__list_task_lists`, `mcp__google_workspace__manage_task_list`
- `mcp__google_workspace__list_tasks`, `mcp__google_workspace__manage_task`

**Default task lists:**
- **Academic** — homework, projects, exams
- **Projects** — personal code/research projects
- **Personal** — life admin

When creating tasks from briefings or conversation, sort into the appropriate list automatically.

### File Writing (local workspace)

- Write to `/workspace/extra/obsidian/` for persistent notes (if mounted)
- Update `/workspace/group/portfolio.md` when portfolio changes
- Append to `/workspace/group/market-log.md` for market snapshots
- Append to `/workspace/group/weekly-review.md` for weekly summaries

## Workflow Examples

### Creating a Study Block

1. Get user's free time via `query_freebusy` for the target date
2. Pick the longest contiguous free block that fits the requested duration
3. Call `manage_event` with a clear `[Course] Study Block` title
4. Confirm to user with the time and a link

### Drafting a Professor Email

1. Draft via `draft_gmail_message` with formal tone
2. Show the draft to the user verbatim
3. Ask "Send as-is, or want changes?"
4. Send only after explicit confirmation

### Weekly Review Doc

1. Gather data from `academic`, `market-intel`, `project-manager` skills
2. Compose a structured doc in memory
3. Create via `create_drive_file` with name `Weekly Review - Week of YYYY-MM-DD`
4. Send the link to the user

## Rules

- **Drafts before sends** for anything outbound
- **Conflict checks** before creating calendar events
- **Confirmation** for destructive or visible actions (email send, calendar create)
- **Label automation** so the user can distinguish Andy's entries from their own

## Output Style

- Concise confirmations after each action: "✅ Created 'CS 477 Study Block' at 7-9pm"
- Links where relevant
- If an action fails, say why and offer an alternative
