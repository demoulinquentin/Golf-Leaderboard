# Golf Tournament Leaderboard — Product Spec

## Location
- Local: `~/Golf-Leaderboard/`
- GitHub: `git@github-personal:demoulinquentin/Golf-Leaderboard.git`
- Hosted: `https://demoulinquentin.github.io/Golf-Leaderboard/`
- SSH alias: `github-personal` (personal account `demoulinquentin`)

---

## 1. Project Scope

**A reusable golf tournament app for small groups.**

| Aspect | Decision |
|--------|----------|
| Purpose | Reusable for any tournament, not specific to one event |
| Players | Max 8 |
| Teams | 2, 3, or 4 teams (fixed for the tournament). Typical: 3 teams of 2 |
| Rounds | Typically 3 rounds on different courses |
| History | One tournament at a time. Export/delete when done. No persistent history. |
| Roles | **Admin** (creator) = full control. **Players** = enter own scores + view all. |
| Identity | Pick your name from list on first visit. No password. No memory (pick every time). |
| Score editing | Players can correct their own past holes (until card is signed). Admin can edit anyone. |
| Courses | Pre-loaded from `courses/` folder in repo. No in-app custom course creation. |
| Backend | Firebase Realtime Database (free tier) — single source of truth |
| Hosting | GitHub Pages (auto-deploy via GitHub Actions) |
| Offline | Not supported — requires internet connection |

---

## 2. Formats

### Supported Formats

| Format | Players per group | Scoring | Team points (configurable) |
|--------|------------------|---------|---------------------------|
| **Stableford** | Any (groups are logistical only) | All players ranked together by stableford score | Position-based. Default: 1st=N pts, last=1 (N=number of players) |
| **Match play** | 1v1, cross-team | Net score per hole, win/halve/lose | Default: win=2, halve=1, loss=0 |
| **Chouette** | 3 players, one per team, cross-team | Head-to-head stableford, 6 pts distributed per hole | Default: 1st=4, 2nd=2, 3rd=0 |
| **Best ball** | 2v2, cross-team | Best net score of the pair | TBD — parked for later |

### Points System
- Every format produces a ranking/result that converts to **team points**
- Points tables are **configurable per format** at tournament creation
- Each player's points contribute to their team total
- Tie-breaking rules: TBD

### Grouping / Pairing
- Admin sets groups/pairings per round at tournament creation
- Groups are max 4 players
- In match play and chouette: always cross-team
- In stableford: groups are logistical only (who walks together), all players ranked together
- Teams are fixed for the tournament, but groups change per day

### Typical 3-team, 6-player Setup

| Day | Format | Groups |
|-----|--------|--------|
| Day 1 | Stableford | Group A: 4 players (Team A+B), Group B: 2 players (Team C) |
| Day 2 | Chouette | Group A: 3 players (1 per team), Group B: 3 players (1 per team) |
| Day 3 | TBD | TBD |

---

## 3. Leaderboards

### Team Leaderboard
- Total team points across all completed rounds
- Shows breakdown per round
- This is the **main competition**

### Player Leaderboard
- Individual points earned across all rounds
- Shows brut score per round
- Ranked by total points

### Day Leaderboard (Journée)
- Format-specific results for the current day:
  - Stableford: player ranking by stableford score + points awarded
  - Chouette: results per group (1st/2nd/3rd + points)
  - Match play: match results + points
- During tournament: shows **today only**
- After tournament ends: all days become browsable

---

## 4. Usability

### First Contact
1. Player opens the shared link
2. Welcome screen: tournament name + teams overview
3. Player picks their name from the list
4. Lands on their group's scorecard for today

### Navigation (Bottom Tab Bar — Icons Only)

| Icon | Tab | Content |
|------|-----|---------|
| Trophy | **Équipes** | Team leaderboard across all rounds |
| Person | **Joueurs** | Player leaderboard + brut per round |
| Calendar | **Journée** | Today's format-specific results |
| Grid A | **Carte A** | Scorecard for Group A of today |
| Grid B | **Carte B** | Scorecard for Group B of today |

- Player **auto-lands on their group's scorecard**
- Carte B tab **disappears** if only 1 group for the day
- Tabs adapt to the current day's format
- Icons only (no text labels) for compact phones

### Score Entry — Scorecard Grid
Looks like a **paper scorecard**:
- Columns: 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 | 9 | OUT | 10 | 11 | ... | 18 | IN | TOTAL
- Rows: Par, SI, Coups reçus, then each player's brut score row
- **Tap a cell** → number picker pops up to enter/edit score
- OUT / IN / TOTAL auto-calculate
- Color-coded cells: eagle (gold), birdie (green), par (neutral), bogey (red), double+ (dark red)
- Horizontally scrollable on mobile
- Players can only edit **their own row** (admin can edit anyone)

### End of Day — Card Signing
1. Player enters hole 18 score
2. App shows: **"Voulez-vous signer votre carte ?"**
3. Summary displayed: full card, brut total, net total, stableford points / match result
4. Player can **go back** to fix mistakes or **confirm to sign**
5. Signed card = **locked** (no more edits for that player)
6. Progress visible: **"4/6 cartes signées"**
7. When all players signed → **day is closed**, results are final
8. Admin can unlock a card if needed

---

## 5. Tech Stack

- Single HTML file with embedded CSS + JS
- Vanilla JS, no frameworks, no build tools
- **Firebase Realtime Database** for real-time sync across devices
- Tournament sharing via URL with hash code (`#ABC123`)
- GitHub Pages hosting with GitHub Actions auto-deploy
- Mobile-first, touch-optimized

### Theme — Augusta National Masters
- Deep Augusta green (`#003d2e`) background
- Gold/yellow (`#ffd700`) accents
- Warm cream text (`#fff8e7`)
- Georgia serif for headings, sans-serif for UI
- Color-coded scores: eagle=gold, birdie=green, bogey=red, double+=dark red

---

## 6. Pre-loaded Courses

Stored in `courses/` folder as JSON files. To add a course: create a JSON file and redeploy.

| Course | Par | File |
|--------|-----|------|
| Golf d'Étretat | 72 | `courses/etretat.json` |
| Golf Barrière de Deauville | 72 | `courses/deauville.json` |
| Golf Barrière de Saint-Julien | 73 | `courses/saint-julien.json` |

### Course JSON Format
```json
{
  "id": "etretat",
  "name": "Golf d'Étretat",
  "par": 72,
  "location": "Étretat, Normandy",
  "holes": [
    { "num": 1, "par": 5, "si": 8 },
    ...
  ]
}
```

---

## 7. Key Algorithms

- **Strokes received**: `floor(hcp/18)` base + 1 extra if `hole.si <= hcp%18`
- **Net score**: brut - strokes received
- **Stableford points**: net vs par → double bogey+=0, bogey=1, par=2, birdie=3, eagle=4, albatross+=5
- **Chouette**: pairwise comparison of stableford scores, 6 pts distributed per hole
- **Match play**: cumulative net comparison per hole, match ends when `|lead| > holesRemaining`

---

## 8. Parked / TBD

- Best ball format details
- Tie-breaking rules for all formats
- Day 3 format specifics
- Exact configurable points tables UI
- Admin unlock card flow
- Post-tournament browsing UI

---

## 9. Source Google Sheet (Reference)
`1nzuUlgz9Kk6DwOCUYLi4ucmUIxHYqBqkWINMCGco-xI` — "Leaderboard - Carnac Major"
Original spreadsheet the app was designed from. Scorecard A layout is the reference for the score entry grid UI.
