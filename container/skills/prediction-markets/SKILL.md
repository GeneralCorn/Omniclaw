---
name: prediction-markets
description: Monitor Kalshi and Polymarket prediction markets for big movers, log daily snapshots, and cross-reference with user portfolio themes. Activate when the user asks about prediction markets, Kalshi, Polymarket, or during scheduled market briefings.
---

# Prediction Markets Tracker

Tracks Kalshi and Polymarket for contracts relevant to Yichen's themes: AI regulation, tech earnings, Fed rates, crypto, elections.

## Data Sources

- **Web search** — headline level, fastest
- **agent-browser** — for structured scraping of market pages
- **Kalshi public pages** — https://kalshi.com/markets/trending
- **Polymarket public pages** — https://polymarket.com

No API key required for read access via browser.

## Focus Contracts

- AI regulation (federal, state, EU AI Act milestones)
- Big Tech earnings beats/misses (NVDA, MSFT, GOOGL, META, AAPL, AMD)
- Fed rate decisions (25/50 bps moves)
- Crypto price bands (BTC > $X by date)
- Election outcomes (when relevant to market impact)

## Scheduled Scrape (7:30 AM daily)

1. Open `https://kalshi.com/markets/trending` via agent-browser
2. `browser_snapshot` to extract market names and prices
3. Do the same for `https://polymarket.com`
4. Diff against `market-log.md` (previous day's snapshot)
5. Flag any contract that moved **> 10% in 24 hours**
6. Append today's snapshot to `market-log.md` with ISO timestamp

## Snapshot Format (market-log.md)

```
## 2026-04-14 07:30 UTC

### Kalshi
| Market                              | Price | Δ 24h  |
|-------------------------------------|-------|--------|
| AI regulation passed by 2026        | 41%   | +7%    |
| NVDA beats Q2 earnings              | 72%   | -3%    |

### Polymarket
| Market                              | Price | Δ 24h  |
|-------------------------------------|-------|--------|
| OpenAI IPO by 2027                  | 45%   | +12%   |

### Big Movers (>10% 24h)
- OpenAI IPO by 2027: 33% → 45% (+12%)
```

## Morning Briefing Inclusion

Only mention big movers (not every contract):

```
🔮 Prediction markets overnight
• "OpenAI IPO by 2027" (Polymarket): 33% → 45%
• "AI reg bill passes" (Kalshi): 41% → 48%
```

## Cross-Referencing with Portfolio

Some prediction markets affect held stocks. Flag the connection:

- AI regulation markets → NVDA, MSFT, GOOGL
- Big Tech earnings markets → same-ticker impact
- Fed rate markets → broad market direction

## Output Style

- Table for snapshots, bullets for briefings
- Always include direction of move (+/-)
- One-line commentary per notable mover
