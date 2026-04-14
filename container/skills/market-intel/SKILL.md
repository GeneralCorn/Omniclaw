---
name: market-intel
description: Monitor AI/tech news, track stock portfolio, watch Kalshi and Polymarket prediction markets, and cross-reference news with holdings. Activate when the user asks about stocks, markets, news, portfolio, prediction markets, or during scheduled market briefings.
---

# Market Intelligence

Personal market intelligence copilot combining AI news, equity tracking, and prediction markets.

## Data Sources

- **Portfolio** — `portfolio.md` in the group folder (`/workspace/group/portfolio.md`). Source of truth for current holdings.
- **Market log** — `market-log.md` in the group folder for daily snapshots and big movers.
- **Web search** (`WebSearch`) for price data, headlines, and prediction market state.
- **agent-browser** for deeper scraping of Kalshi, Polymarket, Yahoo Finance when needed.
- **News API / NewsAPI.org** if `NEWS_API_KEY` env var is set.

## When to Activate

- User asks about stocks, markets, news, portfolio, prediction markets, Kalshi, Polymarket
- Scheduled morning or weekly briefings
- User mentions a ticker or company name
- User says "portfolio" or "news"

## Portfolio Management

Read `/workspace/group/portfolio.md` for current holdings. Format:

```
| Ticker | Shares | Avg Cost |
|--------|--------|----------|
| NVDA   | 10     | 120.50   |
```

When the user shares a new list or CSV, update the file. Never invent holdings.

### Live Price Check

On demand ("portfolio") or during briefings:

1. Read `portfolio.md`
2. Use web search to fetch current prices (Yahoo Finance)
3. Compute daily P&L per holding and total
4. Flag any holding that moved more than 3% intraday

## Watchlist (non-held)

Always include these in price checks for context: **AMD, TSM, AMZN, PLTR**

## AI/Tech News

Sources (in priority order):

1. Hacker News top stories
2. ArXiv cs.AI, cs.LG, cs.CL (for research breakthroughs)
3. TechCrunch AI section
4. NewsAPI.org (if key available)

Focus areas: LLMs, agents, prediction markets, NLP, AI regulation, chip supply.

### Cross-Referencing News ↔ Portfolio

When major AI news drops, check impact on holdings:

- **NVDA** — chip news, AI training demand, export controls
- **MSFT** — OpenAI partnership, Azure AI
- **GOOGL** — Gemini, antitrust, ad revenue
- **META** — Llama, Reality Labs, ad platform
- **AAPL** — Apple Intelligence, App Store
- **AMD** — data center GPUs, AI accelerators
- **TSM** — foundry supply, geopolitical risk

Format cross-reference:

> ⚠️ OpenAI releases GPT-5 → MSFT likely +1-2% (OpenAI partner)
> You hold N shares → estimated impact +$XX

If news is noise with no portfolio impact, say so in one line.

## Prediction Markets

Check Kalshi and Polymarket via web search. Focus contracts:

- AI regulation, chip export controls
- Big Tech earnings beats/misses
- Fed rate decisions
- Crypto price bands

Log interesting movers (>10% overnight) to `market-log.md` with timestamp.

## Scheduled Behaviors

### Morning Briefing (8:00 AM)
- Pre-market prices for holdings + watchlist
- Top 3-5 AI/tech headlines
- Portfolio ↔ news cross-reference
- Prediction market overnight movers

### Weekly Review (Friday 9:00 AM)
- Weekly % change per holding
- Total portfolio move for the week
- AI news recap
- Upcoming earnings for held tickers

## Proactive Alerts

Send proactively (not waiting for briefing) when:

- Portfolio holding drops **>5% intraday**
- AI news directly impacts a specific holding
- A prediction market Yichen follows moves **>20% in 24hrs**

## Output Style

- Short, skimmable bullets
- Discord markdown (`**bold**`, `*italic*`, `[link](url)`)
- Lead with biggest mover or most urgent news
- Never invent prices — use web search
