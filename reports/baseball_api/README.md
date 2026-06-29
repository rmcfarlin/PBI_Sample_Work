# Baseball API — live MLB Stats API report

A Power BI report built **directly on a live REST API** (the public [MLB Stats API](https://statsapi.mlb.com/api/v1/)), pulled and shaped in Power Query (`Web.Contents` → JSON expansion → typed tables) and refreshable on demand. Where [`baseball_light`](../baseball_light/) works a static historical CSV, this one demonstrates **API integration, JSON parsing, and a live refreshable model**.

> Status: **exploration / planning** (scaffold only). Data model and pages to be built next.

## Endpoints explored

Base: `https://statsapi.mlb.com/api/v1`

| Endpoint | Returns | Use |
|---|---|---|
| `/baseballStats` | Catalog of **204 stat definitions** (98 hitting · 109 pitching · 45 fielding · 25 running; 91 counting / 113 rate). Each has `name`, `lookupParam`, `isCounting`, `statGroups`. | The *menu* of available stats — not chart data itself. Could power a "stat glossary" reference visual. |
| `/standings?leagueId=103,104&season=YYYY&standingsTypes=regularSeason` | 6 division records; per team: `wins`, `losses`, `winningPercentage`, `gamesBack`, `divisionRank`, `leagueRank`, `streak`, `clinchIndicator`, `runDifferential` (via `records`). | **Standings dashboard** — W-L bars, win% by team, GB races, run differential. |
| `/stats?stats=season&group=hitting&season=YYYY&sportId=1&playerPool=qualified` | Per-player season hitting — **~35 fields**: `avg`, `homeRuns`, `rbi`, `ops`, `obp`, `slg`, `hits`, `doubles`, `triples`, `stolenBases`, `strikeOuts`, `baseOnBalls`, `totalBases`, `babip`, `plateAppearances`, … | **Live hitting leaderboards + sabermetric scatter** (OPS vs HR, power/contact quadrant). |
| `/stats?stats=season&group=pitching&...` | Per-player pitching — `era`, `whip`, `strikeOuts`, `wins`, `saves`, `inningsPitched`, … | **Pitching leaderboards.** |
| `/stats/leaders?leaderCategories=homeRuns&season=YYYY&sportId=1` | Single-stat top-N (top key `leagueLeaders`). | Quick top-10 lists. |
| `/teams?sportId=1&season=YYYY` | Team metadata: `name`, `abbreviation`, `venue`, `division`, `league`. | Dimension/lookup table for joins + branding. |

Live sample (2024 qualified hitters): Bobby Witt Jr. — .332 / 32 HR / 109 RBI / .977 OPS / 211 H / 31 SB.

## Proposed report (to build)

Reuse the **Ballpark** design system (navy/crimson/gold, Oswald/Libre Franklin) and the SVG-tile/icon language from `baseball_light`.

1. **Standings** — live division standings (W-L bars, win%, GB, run differential), AL/NL split, clinch markers. The "live" hook.
2. **Hitting leaders** — dynamic leaderboard (field-parameter "rank by any stat"), OPS-vs-HR scatter, power/contact quadrant — but on *current* players.
3. **Pitching leaders** — ERA / WHIP / strikeout leaders.
4. **Player / team explorer** — drill-through profile reusing the SVG hero + percentile panels.

## Power Query notes

- Pull each endpoint with `Web.Contents("https://statsapi.mlb.com/api/v1/…")`, then `Json.Document` → navigate to the records/splits list → `Table.FromRecords` / expand → set types.
- Parameterize **season** (and optionally league/group) as a Power Query parameter so the report re-points without edits.
- The `/stats` splits live at `stats[0].splits[].stat` (the measures) + `splits[].player` / `splits[].team` (the keys).
- Standings teams live at `records[].teamRecords[]`, with the division at `records[].division.id`.
- Rate stats arrive as **strings** (".332", ".977") — convert to decimal in PQ.
