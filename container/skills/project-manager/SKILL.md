---
name: project-manager
description: Track GitHub repositories, commit activity, open PRs, stale branches, and project TODOs. Activate when the user asks about repos, projects, GitHub status, or during weekly reviews.
---

# Project Manager

Tracks the health and activity of Yichen's personal projects via GitHub.

## Data Sources

- **GitHub MCP** (`mcp__github__*`) — when `github` MCP server is configured with a PAT
- **Web search** fallback if GitHub MCP is not available
- **Local workspace** — `/workspace/group/` for project TODOs and notes

## When to Activate

- User asks about projects, repos, GitHub, PRs, commits
- User says "what should I work on" (combined with `academic` for priority)
- Friday weekly review
- On-demand: "project status" or "git status"

## Key Projects

Update this list as the user tells you about new repos:

- **Nanoclaw Agent** — this bot itself
- **Kalshi/prediction market trading bot** — if exists
- **Framelight** — if referenced
- **Browser extension** — if referenced

## Tracked Metrics

For each repo (via GitHub MCP):

- **Commits this week** — `mcp__github__list_commits` filtered by date
- **Open PRs** — both authored and review-requested
- **Open issues** — count and list titles
- **Last commit timestamp** — flag if > 14 days (stale)
- **Default branch health** — any failing CI checks

## Weekly Review Output

Include in Friday 9:00 AM briefing:

```
📂 **Projects this week**
• nanoclaw: 12 commits, 2 PRs merged, 0 stale
• kalshi-bot: 0 commits — stale for 18 days ⚠️
• framelight: 4 commits, 1 open PR
```

## On-Demand Commands

### "project status"

Quick snapshot of all tracked repos. One line per repo:
- Commits in last 7 days
- Open PRs
- Last activity timestamp

### "what should I work on" (joint with academic skill)

Prioritize across:
1. Academic deadlines (from `academic` skill)
2. Stale but important projects (from this skill)
3. Open PRs awaiting review

Present as a ranked list with 🔴🟡🟢 urgency labels.

## Behavior

- If GitHub MCP is not configured, say so once and fall back to web search
- Never modify repos (no commits, PRs, or issue changes) unless explicitly asked
- When user asks "why is X stale", check for uncommitted local work via `git status` in mounted project directories

## Output Style

- Tables or bulleted lists, not paragraphs
- Include commit hashes for recent notable commits
- Discord markdown formatting
