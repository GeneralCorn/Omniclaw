---
name: cumtd-bus
description: Check real-time Champaign-Urbana Mass Transit District (CUMTD) bus departures. Activate when the user asks about buses, commute, when to leave for class, or nearby transit.
---

# CUMTD Bus Tracker

Real-time bus departures for Champaign-Urbana. Free public API with registration.

## Prerequisites

- `CUMTD_API_KEY` environment variable (free from https://developer.cumtd.com/)
- If the key is missing, tell the user once and fall back to agent-browser on https://mtdbuses.com or https://cumtd.com.

## API Reference

Base URL: `https://developer.cumtd.com/api/v2.2/json/`

Key endpoints:

- `GetDeparturesByStop?key={KEY}&stop_id={STOP_ID}` — next departures from a stop
- `GetStop?key={KEY}&stop_id={STOP_ID}` — stop metadata
- `GetStopsBySearch?key={KEY}&query={QUERY}` — find a stop by name

## Default Stops

Maintain `cumtd-stops.md` in the group folder with user's usual stops. Example format:

```
| Label         | Stop ID    | Notes             |
|---------------|------------|-------------------|
| Home          | XXXXXXX    | Near apartment    |
| Campus - Main | XXXXXXX    | Closest to Siebel |
```

If the file doesn't exist, ask the user for their usual stops and create it.

## Usage Examples

### Quick check — next 3 departures from a stop

```bash
curl -s "https://developer.cumtd.com/api/v2.2/json/GetDeparturesByStop?key=${CUMTD_API_KEY}&stop_id=${STOP_ID}&count=3" \
  | jq '.departures[] | {route: .headsign, expected: .expected_mins}'
```

### "when should I leave for class?"

1. Get user's next class from Google Calendar via `mcp__google_workspace__get_events`
2. Read home stop from `cumtd-stops.md`
3. Call `GetDeparturesByStop` for the home stop
4. Estimate walk time to stop (default: 5 min)
5. Pick the latest departure that still arrives before class start
6. Reply: "Leave by H:MM — next [Route] at H:MM arrives [destination] at H:MM"

### Morning briefing inclusion

Include the next 1-2 campus-bound departures in the 8 AM briefing:

```
🚌 Next 22 Illini to campus: 8:42 AM (in 12 min)
```

## Behavior

- If the next departure is > 60 minutes out, skip mentioning it (probably off-peak).
- If the bus is delayed (expected_mins much larger than scheduled), flag it.
- For "leave now" calculations, add a 2-minute buffer to walk time.

## Output Style

- One line per departure unless user asks for more detail
- `🚌 {Route} — {expected_mins} min`
- Include delays only when noteworthy
