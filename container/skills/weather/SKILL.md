---
name: weather
description: Fetch current and forecast weather via OpenWeatherMap. Activate when the user asks about weather, when planning outdoor events, or during morning briefings.
---

# Weather

Weather data via OpenWeatherMap for briefings and event planning.

## Prerequisites

- `OPENWEATHER_API_KEY` environment variable (free tier from https://openweathermap.org/api)
- If missing, fall back to web search for weather summary.

## Default Location

Stored in `weather-location.md` in the group folder. If missing, ask user for their city and save it. Default during UIUC term: **Champaign, IL, US**.

## API Reference

Base URL: `https://api.openweathermap.org/data/2.5/`

- `/weather?q={CITY}&appid={KEY}&units=imperial` — current conditions
- `/forecast?q={CITY}&appid={KEY}&units=imperial` — 5-day / 3-hour forecast

## Usage

### Quick current conditions

```bash
curl -s "https://api.openweathermap.org/data/2.5/weather?q=Champaign,IL,US&appid=${OPENWEATHER_API_KEY}&units=imperial" \
  | jq '{temp: .main.temp, desc: .weather[0].description, feels_like: .main.feels_like}'
```

### Rain check for a specific time window

1. Fetch 5-day forecast
2. Find the 3-hour buckets overlapping the target window
3. Check `weather[0].main` for Rain/Snow/Thunderstorm
4. If precipitation expected, warn the user

## Morning Briefing Inclusion

```
🌤️ Weather: 65°F, partly cloudy. 30% chance of rain after 3pm — bring umbrella.
```

## Proactive Behaviors

- When creating a calendar event marked "outdoor" or in a park, check forecast and warn if rain is expected during the event.
- When user has a morning class and forecast shows rain during commute window, suggest earlier departure.

## Output Style

- One or two short lines for briefings
- Temperature in °F (imperial units)
- Condition emoji: ☀️ clear, ⛅ partly cloudy, ☁️ cloudy, 🌧️ rain, ⛈️ storm, ❄️ snow
