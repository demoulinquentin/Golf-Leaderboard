# Implementation Plan

## Phase 1 — Foundation

### Initiative 1: Data Model Refactor
- [ ] Add `adminId` to tournament (creator)
- [ ] Add `currentDay` index for active day tracking
- [ ] Add `signed` map: `{ "d{day}_{playerId}": true/false }`
- [ ] Add `pointsConfig` per format (configurable tables)
- [ ] Add `currentPlayerId` in localStorage (from welcome screen)
- [ ] Embed courses from `courses/` JSON files into app code
- [ ] Remove localStorage fallback (Firebase = single source of truth)
- [ ] Remove custom course editor from wizard

### Initiative 2: Welcome Screen & Player Identity
- [ ] Welcome screen: tournament name + teams overview
- [ ] Player picks name from list
- [ ] No "remember me" — always show on open
- [ ] Admin identified by `currentPlayerId === tournament.adminId`
- [ ] Permission gating: players edit own scores only, admin edits all

### Initiative 10: Setup Wizard Rework
- [ ] Step 1: Tournament name
- [ ] Step 2: Teams + players (name + handicap)
- [ ] Step 3: Rounds (course selection from pre-loaded, format per round)
- [ ] Step 4: Groups/pairings per round + points config
- [ ] Creator = admin automatically

## Phase 2 — Core UX

### Initiative 3: Scorecard Grid Rewrite
- [ ] Paper scorecard layout: columns 1-9 | OUT | 10-18 | IN | TOTAL
- [ ] Rows: Par, SI, Coups reçus, player brut scores
- [ ] Tap cell → number picker popup
- [ ] Players edit own row only (admin edits anyone)
- [ ] OUT/IN/TOTAL auto-calculate
- [ ] Color-coded cells (eagle/birdie/par/bogey/double+)
- [ ] Horizontal scroll on mobile
- [ ] Adapts to day's format

### Initiative 4: Navigation Overhaul
- [ ] Replace text tabs with icon-only bottom bar
- [ ] Tabs: Équipes (trophy), Joueurs (person), Journée (calendar), Carte A (grid), Carte B (grid)
- [ ] Carte B disappears when only 1 group
- [ ] Player auto-lands on their group's scorecard
- [ ] Config accessible via gear icon (admin only)

## Phase 3 — Scoring & Formats

### Initiative 7: Points System
- [ ] Configurable points tables per format in wizard
- [ ] Defaults: stableford=position-based, match play=2/1/0, chouette=4/2/0
- [ ] Auto-calculate from day results
- [ ] Feed into team & player leaderboards

### Initiative 8: Chouette Format
- [ ] Groups of 3 (one per team)
- [ ] Pairwise stableford: 6 pts per hole
- [ ] Distribution: all different=4/2/0, two tie best=3/3/0, two tie worst=4/1/1, all tie=2/2/2
- [ ] Configurable team points (default 4/2/0)
- [ ] Scorecard shows 3 players with chouette running totals

### Initiative 5: Team & Player Leaderboards
- [ ] Équipes tab: team points total + breakdown per round
- [ ] Joueurs tab: individual points + brut per round
- [ ] Both real-time updated

### Initiative 6: Day Leaderboard (Journée)
- [ ] Stableford: player ranking + points awarded
- [ ] Chouette: results per group
- [ ] Match play: match results + points
- [ ] During tournament: today only
- [ ] After tournament: browse all days

## Phase 4 — Polish

### Initiative 9: Card Signing Flow
- [ ] Prompt after hole 18: "Voulez-vous signer votre carte ?"
- [ ] Show round summary (brut, net, points/result)
- [ ] Go back to fix or confirm to sign
- [ ] Signed = locked (no edits)
- [ ] Progress: "4/6 cartes signées"
- [ ] All signed → day closed, results final
- [ ] Admin can unlock a signed card
