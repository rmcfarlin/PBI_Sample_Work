# Baseball API — live MLB Stats API report

A Power BI report built **directly on a live REST API** (the public [MLB Stats API](https://statsapi.mlb.com/api/v1/)), pulled and shaped in Power Query (`Web.Contents` → JSON expansion → typed tables) and refreshable on demand. Where [`baseball_light`](../baseball_light/) works a static historical CSV, this one demonstrates **API integration, JSON parsing, a parameterized season, and a refreshable star-schema model**.

> Status: **built** — open `Baseball API.pbip` in Power BI Desktop and **Refresh** to pull seasons **2020–2025**. No API key required.

## The model

Four source tables built from the API, two dimensions, a measure table, and two field parameters — a clean star with **Teams** and **Seasons** as the shared dimensions.

| Table | Endpoint | Grain | Notes |
|---|---|---|---|
| **Teams** | `/teams?sportId=1&season={SeasonEnd}` | one row per MLB club | dimension: name, abbrev, division, league, venue |
| **Seasons** | — (`{SeasonStart}…{SeasonEnd}`) | one row per season | dimension driving the season slicer on every page |
| **Standings** | `/standings?leagueId=103,104&season={yr}` | one row per team-season | W-L, win %, run differential, streak, games back |
| **Hitting** | `/stats?group=hitting&playerPool=qualified&season={yr}` | one row per hitter-season | ~25 fields incl. AVG/OBP/SLG/OPS/ISO |
| **Pitching** | `/stats?group=pitching&playerPool=qualified&season={yr}` | one row per pitcher-season | ERA, WHIP, K, K/9, K/BB, saves |

- **`SeasonStart` / `SeasonEnd`** are Power Query parameters (default `2020` / `2025`). The fact queries loop the API once per season in that range (`List.Numbers` → `Table.Combine`) and stamp a `Season` column; change the bounds and the whole model re-points. Extend `SeasonEnd` to `2026` to fold in the live, in-progress season.
- Every page carries a **single-select season slicer** (defaults to 2025) so leaderboards, standings, and the explorer always read one clean season; the SVG KPI tiles are season-aware (they rank within the selected season).
- Rate stats arrive from the API as **strings** (`".332"`, `".977"`) and are converted to decimal in Power Query with invariant culture.
- `Standings`, `Hitting`, and `Pitching` relate to `Teams` on `TeamId` and to `Seasons` on `Season` (single-direction, dimensions → facts).
- **41 measures** (counting, rate, dynamic, and SVG) plus **Rank By Hitting** / **Rank By Pitching** field parameters drive the dynamic leaderboards.

## Pages

1. **Standings** — four SVG KPI tiles (best record, most runs, best run differential, HR leader) over a division standings table and a wins-by-team bar.
2. **Hitting Leaders** — *rank the field by any stat* (field parameter): a Top-15 bar with a dynamic title, a HR × OPS power-vs-production scatter, and a qualified-hitters table.
3. **Pitching Leaders** — same pattern for pitchers (Wins / K / Saves / K9 / K-BB / batters faced), a K/9 × ERA scatter, and a qualified-pitchers table. ERA and WHIP are deliberately kept out of the "rank by" list (lower-is-better stats don't rank descending) and shown in the scatter/table instead.
4. **Explorer** — a full-height navy navbar player picker, an SVG hero with the player's season line, a season-at-a-glance KPI strip, and a detailed season-line table.

Styled with the shared [Ballpark](../../design_systems/ballpark/) design system (navy `#0E3386` / crimson `#CC3433` / gold `#C8A24B`, Oswald + Libre Franklin) and the same SVG-tile / image-visual language as `baseball_light`.

## Power Query notes

- Each table: `Json.Document(Web.Contents("…/{endpoint}", [Query=[…, season=Season]]))` → navigate to the records/splits list → `Table.FromList` → `ExpandRecordColumn` → type.
- `/stats` splits live at `stats[0].splits[]` — `[].stat` holds the measures, `[].player` / `[].team` the keys.
- Standings teams live at `records[].teamRecords[]`, with the division/league ids on the parent `records[]` record.
- The model validates against the TOM `TmdlSerializer` (the authoritative Desktop-engine check): 8 tables, 87 columns, 41 measures.
