# Golf Tournament Leaderboard

A standalone web app for managing golf tournament scores, leaderboards, and match play — designed to run on your phone during a round.

**No server, no dependencies, no internet required.** Just open `index.html` in a browser.

## Features

### Setup Wizard (4 steps)
1. Tournament name + number of days
2. Teams/individual mode, players (name + handicap), max 8
3. Course selection (3 pre-loaded) + custom course editor (hole-by-hole par & SI)
4. Per-day config: course, format, groups or matchups

### 5 Tabs

| Tab | Description |
|-----|------------|
| **Classement** | Leaderboard with round selector. Adapts to format. Team subtotals. Tap player for scorecard. |
| **Scores** | Per-hole entry: large tap buttons, strokes received, hole navigator, auto-detects current hole |
| **Cartes** | Full 18-hole scorecard: Par, SI, Coups reçus, Brut, Net (+ Points for Stableford). Color-coded cells. |
| **Matchs** | Match cards with live status (X UP / AS / X & Y). Hole-by-hole won/lost/halved dots. Team aggregate. |
| **Config** | Edit tournament, teams, players, export/import JSON, reset, launch wizard |

### Supported Formats
- **Stroke play** — ranked by net +/- (tiebreak: brut)
- **Stableford** — ranked by total points descending
- **Match play** — net score comparison per hole, early termination when lead > remaining holes

### Pre-loaded Courses

| Course | Par | Location |
|--------|-----|----------|
| Golf d'Étretat | 72 | Normandy |
| Golf Barrière de Deauville | 72 | Normandy |
| Golf Barrière de Saint-Julien | 73 | Normandy |

## Tech Stack
- Single HTML file with embedded CSS + JS
- Vanilla JS — no frameworks, no build tools
- Data persisted in `localStorage`
- Works fully offline
- Mobile-first, touch-optimized

## Theme
**Augusta National Masters** inspired:
- Deep Augusta green background
- Gold/yellow accents
- Georgia serif headings
- Color-coded scores: eagle (gold), birdie (green), bogey (red), double+ (dark red)

## Data Model

```
Tournament {
  name, teams[]{id, name, players[]{id, name, handicap}},
  courses[]{id, name, par, holes[]{num, par, si}},
  days[]{label, courseId, format, groups[][], matches[]{player1, player2}},
  scores: { "d{dayIndex}_{playerId}": [18 values or null] }
}
```

## Key Algorithms
- **Strokes received**: `floor(hcp/18)` base + 1 extra if `hole.si <= hcp%18`
- **Net score**: brut - strokes received
- **Stableford**: net vs par → 0/1/2/3/4/5 points
- **Match play**: cumulative net comparison, match ends when `|lead| > holesRemaining`

## Usage

1. Open `index.html` in any browser
2. Follow the setup wizard to configure your tournament
3. Enter scores hole-by-hole during the round
4. Use **Config > Export JSON** to backup your data

## Known Issues / TODO
- Leaderboard missing Net column on stroke play view
- Config tab may be cut off on narrow screens
- No PWA support yet (Add to Home Screen)
- No multi-device sync (each phone has its own localStorage)
- Halved matches don't count as 0.5 pts in team scoring
