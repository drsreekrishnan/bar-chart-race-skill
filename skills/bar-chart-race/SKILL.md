---
name: bar-chart-race
description: >
  Creates an AI-powered, animated Bar Chart Race visualization that lets users
  compare how any set of entities (companies, countries, products, technologies,
  sports teams, etc.) have evolved over time. Use this skill whenever the user
  wants to visualize rankings or relative sizes changing over time — even if they
  don't use the exact words "bar chart race". Trigger for requests like:
  "show me how X has grown compared to Y over the years", "visualize the
  rise of tech companies", "which countries had the biggest GDP over time",
  "animate the history of streaming services", "compare programming language
  popularity over time", "show market share changes", "who was winning the
  smartphone wars each year", or any request involving a time-based ranking
  competition between multiple entities. Always use this skill when the user
  wants a dynamic, animated comparison across years — don't just describe what
  the chart would look like, actually build it.
---

# Bar Chart Race Skill

Produces a fully self-contained, interactive HTML artifact that animates a
racing bar chart of the user's chosen topic. Claude (via the Anthropic API)
generates historically-grounded time-series data on the fly — no external
datasets required.

---

## API Key — when is it needed?

| Context                                          | API key needed? |
|--------------------------------------------------|-----------------|
| Rendered as an artifact inside Claude.ai         | No — injected automatically by Claude.ai |
| HTML file opened directly in a browser           | Yes — user must enter their own key |

The launcher HTML includes an API key input field for the standalone browser
case. When rendered as a Claude.ai artifact, that field is still visible but
can be left blank — the key is provided by the environment automatically.

Users can get a free API key at: https://console.anthropic.com/keys

---

## When to trigger

Trigger on any request matching these patterns (partial list):

| User says…                                        | What to do            |
|---------------------------------------------------|-----------------------|
| "bar chart race for X"                            | Build it directly     |
| "show me how X changed over time"                 | Build it              |
| "animate the history of X"                        | Build it              |
| "compare X vs Y vs Z across the years"            | Build it              |
| "who dominated X industry over the decades"       | Build it              |
| "visualize the rise and fall of X"                | Build it              |
| "which X was biggest each year"                   | Build it              |
| "make a race chart" / "racing bar chart"          | Build it              |
| User pastes a CSV of time-series data             | Build it with their data |

If the user's intent is clearly a time-ranked comparison, **build the artifact
immediately** — don't ask clarifying questions unless the topic is genuinely
ambiguous. Make reasonable assumptions and proceed.

---

## How to build it

### Option A — Interactive launcher (recommended for open-ended requests)

Use the bundled `bar-chart-race.html` as the artifact. This gives the user
a polished landing page where they can:

1. Optionally enter an Anthropic API key (only needed outside Claude.ai)
2. Type or edit the topic themselves
3. Pick from built-in example prompts
4. Hit Generate — the app calls the Anthropic API to generate data and
   renders the race automatically

Best for: "make me a bar chart race tool", "I want to explore different topics"

### Option B — Direct chart (recommended when topic is explicit)

When the user specifies a clear topic ("top 10 GDP countries 1970–2025"),
generate the data yourself using your knowledge and hardcode it directly
into the artifact, bypassing the AI data-generation step entirely.

Data shape to produce:
```json
{
  "title": "Top 10 Economies by GDP",
  "subtitle": "Source: World Bank WDI · IMF WEO projections 2024–25",
  "unit": "Billions USD",
  "yearStart": 1970,
  "yearEnd": 2025,
  "entities": [
    {
      "name": "United States",
      "data": { "1970": 1075, "1971": 1165, ... }
    }
  ]
}
```

Rules for data:
- 10–14 entities is ideal (more clutters the chart)
- Include every integer year in the range; use null for unavailable years
- Values must be a consistent unit (don't mix billions and millions)
- For projections (future years), use reasonable estimates; note in subtitle
- Prefer a 20–55 year range for compelling visual storytelling

---

## Chart engine summary

The rendering engine works as follows:

- rAF loop advances a fractional year counter at 1200 ms/year
- gdpAt(entity, fracYear) linearly interpolates between integer-year values
  — this is what makes bars flow smoothly instead of jumping discretely
- D3 .join() handles enter/update/exit; no D3 transitions are used (the rAF
  interpolation replaces them entirely, giving perfectly fluid 60fps motion)
- Controls: Pause/Play, Replay, year scrubber, keyboard Space/R

---

## Aesthetic spec

```
Background:    #0a0a0f  (near-black)
Surface:       #111118
Border:        #1c1c2e
Accent:        #f97316  (orange — buttons, year display, slider thumb)
Text:          #e8e8f0
Subtext:       #6868a0
Font display:  Bebas Neue (year counter, title)
Font body:     DM Sans   (entity labels)
Font data:     DM Mono   (values, ranks, badges)
Bar height:    70% of row gap
Bar radius:    4px
Bar opacity:   0.88
Speed:         1200 ms/year
```

Color palette for entities (cycle through in order):
```
#f97316  #ef4444  #3b82f6  #10b981  #a78bfa  #ec4899
#f59e0b  #14b8a6  #06b6d4  #84cc16  #f43f5e  #8b5cf6
#fb923c  #34d399  #60a5fa  #e879f9
```

---

## Value formatting

Auto-scale based on magnitude:

| Value range    | Format example  |
|----------------|-----------------|
| >= 1,000,000   | 142.5M          |
| >= 1,000       | 14.3K           |
| >= 100         | 842             |
| < 100          | 42.7            |

Always show unit in the controls area (e.g. "Unit: Billions USD").

---

## Layout

```
┌─────────────────────────────────────────────────────┐
│ LABEL (orange, uppercase)          YEAR  [New chart] │
│ TITLE (Bebas Neue, large)          NNNN              │
│ subtitle (small, muted)                              │
├─────────────────────────────────────────────────────┤
│                                                      │
│  #1  ████████████████████████████  42.5T  EntityA  │
│  #2  ████████████████████          18.2T  EntityB  │
│  ...                                                 │
│                                                      │
│  $5T    $10T    $15T    $20T    $25T                │
├─────────────────────────────────────────────────────┤
│ [Pause]  [Replay]  Unit: $B            1200ms/yr   │
│ 1970 ──────●─────────────── 2025                   │
└─────────────────────────────────────────────────────┘
```

SVG margins: { top: 8, right: 175, bottom: 20, left: 148 }

---

## Edge cases

| Situation                              | Handling                                        |
|----------------------------------------|-------------------------------------------------|
| Entity appears mid-range (e.g. Russia) | Use null before it exists; fades in naturally   |
| Two entities very close in value       | Both visible; rank swap happens smoothly        |
| User provides CSV data                 | Parse it; map to entity data shape above        |
| Very long entity names                 | Truncate to ~22 chars with ellipsis in SVG      |
| < 10 entities available for a year     | Show only available ones; don't pad             |
| yearStart === yearEnd                  | Show static chart, disable animation            |
| API key missing in browser context     | Red border on key field + focus; clear error    |
| Invalid API key (401)                  | Show "Invalid API key" error with dismiss       |
| Rate limit hit (429)                   | Show "Wait a moment and try again" error        |
