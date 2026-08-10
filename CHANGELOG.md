# Basketball Stats App — Changelog

---

## v2.7 — 2026-08-10
- Glossary corrections and additions:
  - Fixed CJB definition: "conceded possession in a jump ball" → "conceded a jump ball while in possession"
  - Rotations section: replaced separate OffPoss/DefPoss rows with a single Poss row
  - Added italic asterisk note under DTO: turnover credited to passer even if receiver fumbles a clean pass
  - Added italic asterisk note under CJB: CJB credited to player responsible for the initial pass/lost ball, not necessarily who held it at the whistle
  - Added **Game Log** section explaining ↑ (insert above), ↓ (insert below), and 🗑 (delete) controls
  - Added ▲/▼/⏫ player order arrows entry to Stat Entry section
  - Increased `?` glossary button size (from `text-xs p-1.5` to `text-sm p-2` with fixed `w-8 h-8`)

## v2.6 — 2026-08-10
- Added **Glossary** — a `?` button next to the dark mode toggle on the game screen opens a scrollable popup covering five sections: Stat Entry buttons (2PM/FTM/LTO/DTO/FJB/OoB/SUB etc.), Box Score columns (REB breakdown, TO/JB breakdowns, O/DRate, Poss), Rotations table (OffPoss/DRate/NetRate/REB±/TO± etc.), Shot Chart grades (A/B/C), and Summary stats (Trans Pts, ½ Court Pts, 2nd Chance, PACE, Largest Lead). Dismisses by clicking the ✕ or tapping the backdrop.

## v2.5 — 2026-08-10
- Rotations JPG and Summary JPG downloads no longer navigate away from the Box Score screen. Both now render into a hidden off-screen container using `ReactDOM.createRoot`, capture with html2canvas, and trigger the download — all without changing the visible screen.
- Added **Download All** option at the top of the Downloads dropdown (end-game view). Downloads Box Score JPG → Rotations JPG → Summary JPG → Game CSV in sequence, all in-place.

## v2.4 — 2026-08-10
- Widened stat buttons on the game screen (opponent and all player cards). All buttons in each of the three sections (shots / mid-stats / TOs + rebounds) now use `flex-1` to fill the full width of their column, eliminating the blank gaps between groups. Removed `justify-center` and `justify-end` from the middle and right sections so buttons spread across the available space instead of clustering.

## v2.3 — 2026-08-08
- Fixed **and-one FT possession attribution**: after a made basket, `possTeamRef` switches to the opponent. If the scorer gets a free throw (and-one), the FT was being committed as the opponent's possession. Fix: `possTeamRef` is now explicitly set to `pending.forTeam` before each FT-pending commit, so the possession is always credited to the team that earned the FTs.
- Fixed **Total vs period view possession/rating mismatch**: the Summary screen was using the exact tracker for Total but a formula estimate (`FGA + TO + 0.44×FTA − OReb`) for period filters, causing different numbers when toggling. Fix: `commitPossession` now writes per-period `offPoss`/`defPoss`/`offPoints`/`defPoints` to `game.periodPoss[period]`, and Summary period view reads from that tracker data instead of the formula. The formula remains as a fallback for games recorded before this version.

## v2.2 — 2026-08-08
- Added **tip-off winner selection** to the initial lineup flow. After confirming starters, the modal transitions to a "Who wins the tip?" screen with two large buttons (your team / opponent). Pressing one sets first possession to the winner, assigns the jump ball arrow to the loser, and starts the tip-off clock. This eliminates the need for the first-jump-ball popup and ensures possessions are attributed to the correct team from tip-off.
- Fixed **DefReb possession overcount**: recording a defensive rebound immediately after a made basket (e.g. scoring possession → DefReb) no longer commits an extra possession. The no-pending DefReb branch now only commits and restarts possession when the defensive rebounder's team doesn't already hold possession.

## v2.1 — 2026-08-08
- Fixed possession undercounting when both teams shoot free throws in consecutive possessions. When a pending `ftMade` for one team was active and the other team recorded a FT (Made or Miss), the pending possession was silently dropped and overwritten. Fix: added a team-change check inside the `ftMade` pending block — if the incoming FT belongs to a different team, the previous possession is committed and a new one started before processing the current FT.

## v2.0 — 2026-08-08
- Added **Player Event Log** to the individual shot chart view (accessed via the "View" button on a player's box score row). Shows all game log entries for that player in chronological order (oldest → newest) below the Shot Quality Breakdown, with Q period, wall clock time, and elapsed time from tip-off.

## v1.9 — 2026-08-08
- Starting lineup no longer pops up automatically when a game begins. A teal **Set Starting Lineup** button now appears in the game bar (left of OoB buttons) and opens the modal on demand. The button disappears permanently once Tip Off is pressed.
- Added an **✕ close button** to the Set Starting Lineup popup so it can be dismissed without triggering the tip-off clock. Period-transition lineup popups are unaffected (no close button — still required).

## v1.8 — 2026-08-08
- Added video-sync timestamps to the Game Log. Each entry now shows the device wall clock time (HH:MM:SS) and elapsed time from tip-off (`+M:SS`).
- Renamed "Confirm Lineup" button to **Tip Off** for the initial lineup only (period-transition lineups still show "Confirm Lineup"). Pressing Tip Off sets t=0 for the elapsed clock.
- Elapsed time for resumed games is restored from the auto-save snapshot so the clock survives page refreshes.
- For historical games viewed after the fact, elapsed is computed from the earliest log entry as a fallback.
- Added a **CSV** download button to the Game Log screen. Exports Period, Wall Clock, Elapsed, and Stat text for every entry, oldest-first.

## v1.7 — 2026-08-07
- Fixed OoB possession counting: when same team retains (OoB-Us while we had ball, or OoB-Opp while they had ball), no new possession is counted. Possession change only fires when the ball switches teams.
- Confirmed offensive rebounds correctly do not start a new possession (OffReb clears pending fgMiss state and returns early).

## v1.6 — 2026-08-07
- Added opponent FT popup parity: opponent FTMade popup now includes **2nd Chance** (manual override) and **Technical (no possession change)** options, matching the our-team FT popup. Previously the opponent popup only had Transition and ½ Court regardless of stat type.

## v1.5 — 2026-08-07
- Recalibrated Summary screen bar chart reference maxima (REF_MAX) based on analysis of 45 real games.
  - Major corrections: Steals (8→28 game / 4→10 period), OffReb (10→25 / 4→10), LiveTO (12→28 / 5→10), DefReb (20→30 / 8→12), DeadTO (8→18 / 4→5), Fouls (15→22 / 6→8), Lead (20→30 / 14→18).
  - Shooting %: FG% ref lowered from 65→50 (teams average 26%), FT% from 90→80.
  - Off/Def Rating ref tightened from 120→100 (actual max was 93.8).

## v1.4 — 2026-08-07
- Moved OoB-Us and OoB-Opp buttons to the left of the ⇄ Poss button.
- Added a small spacer gap between ⇄ Poss and End Q buttons.

## v1.3 — 2026-08-07
- Added **OoB-Us** (green) and **OoB-Opp** (rose) buttons to the game screen next to ⇄ Poss. Used for out-of-bounds situations without a rebound (e.g. ball bounces off rim or hands directly out). Commits/starts possession only when the ball changes teams; same-team retention is a no-op on possession count.
- Added `oob` stat type to undo and delete-stat handlers so these log entries can be cleanly removed.

## v1.2 — 2026-06-25
- Updated PACE formula in Summary screen to Basketball Reference standard:
  `Poss = 0.5 × ((TmFGA + 0.4×TmFTA − 1.07×(TmORB/(TmORB+OppDRB))×TmFGMiss + TmTOV) + (opponent equivalent))`
  `Pace = 48 × Poss / clockMins`
  PACE uses this independent formula only; all other possession stats continue using the existing method.
- Clock minutes for Total view now uses `maxPeriod × minutesPerPeriod` (completed periods only).

## v1.1 — 2026-06-25
- Added pull-to-refresh prevention: `overscroll-behavior-y: none` on html/body stops the browser's native pull-to-refresh gesture from wiping an in-progress game.
- Added in-progress game auto-save: game state (stats, log, rotations, period, lineup, possession) is written to localStorage after every stat. On next app load, a yellow "Game in progress" banner appears on the Home screen with a Resume button. Auto-save is cleared on End Game or intentional Main Menu exit.

## v1.0 — 2026-06-25
- Added single-game export button (teal download icon) to each row in the Previous Games list. Downloads a JSON file compatible with the existing Import function.
- Updated rotation CSV export columns: added OffPoss and DefPoss as separate columns (previously combined as Poss), added raw FGM/FGA for both team and opponent alongside the percentage, removed Rotation # column, signed NetRate with +/-.

---

*Prior to v1.0 (pre-changelog):*
- Fixed FTM/FTX button overlap at the 640px (sm:) breakpoint — reduced narrowShoot padding and 2PM/2PX inline button padding.
- Fixed jump ball possession arrow showing wrong team during game: `processPossession` was ignoring `jbPossTeam` from `extras` in the ForceJB/ConcedeJB branch.
- Fixed period-end transition ignoring the jump ball alternating-arrow: `endPeriod` now consults `jumpBallArrowRef` instead of blindly flipping `possTeamRef`.
