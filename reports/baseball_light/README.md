# Baseball — Career Hitting Leaders

A Power BI report on 465 of the game's best hitters, built end-to-end from a flat Kaggle dataset: a clean semantic model, a full sabermetrics measure library, and a four-page interactive report styled with a custom Chicago Cubs design system.

This is a portfolio piece. The point isn't the baseball trivia — it's the workmanship: correct DAX that aggregates the way rate stats actually should, a dynamic report that re-ranks itself on any metric, and a design system applied through a theme rather than bolted onto each visual.

> **Open it:** `baseball_light.pbip` in Power BI Desktop. The model and report are self-contained (the CSV is imported), so no gateway or published dataset is required. The design system uses **Oswald** and **Libre Franklin** — install those Google Fonts for the intended look (Power BI falls back gracefully if they're missing).

---

## The data

One row per player, career regular-season totals. Source: `../../data/baseball_hits.csv` (465 players, 16 raw columns).

`PLAYER, YRS, G, AB, R, H, 2B, 3B, HR, RBI, BB, SO, SB, CS, BA, HOF`

A couple of things the data does *not* hand you, which the model recovers:

- **The `HOF` flag isn't binary.** It carries `0` (313 players), `1` (151), and `2` (a single player — Tim Raines). Every Hall-of-Fame measure uses `HOF >= 1` so the inducted and non-inducted buckets reconcile to the full 465. A naïve `HOF = 1` silently drops a player and the totals stop adding up.
- **No HBP or sacrifice flies.** On-base percentage is therefore approximated as `(H + BB) / (AB + BB)` and labelled `(approx)` everywhere it appears. OPS inherits that approximation; slugging and ISO are exact.

---

## The four pages

| Page | What it answers |
|---|---|
| **Leaderboard** | Who leads — by *any* metric. A "Rank By" field parameter re-ranks the whole page (bar chart, title, table) across 14 stats in one click. KPI cards for the headline counts; the table carries inline HOF badges. |
| **The Hall of Fame Line** | What separates Cooperstown from the field. The signature Hits-vs-HR scatter coloured by HOF status; induction rate by hit-total and OPS band; a sortable, badge-marked table to hunt snubs and compilers. |
| **Player Profile** | One career, in full. Drill-through target keyed on player — a dynamic narrative line, every counting stat, derived sabermetrics, and a **percentile panel** (SVG) showing where the player ranks across the field, plus archetype/HOF/Cubs identity icons. |
| **Archetypes & Cubs Legends** | Every career placed on a power-vs-contact map (split at the dataset medians), and the Chicago Cubs greats in the data called out with the Cubs "C" roundel and archetype pills. |
| **Drill-Down Explorer** | A matrix that expands archetype → Hall-of-Fame status → player, with counts, hits, home runs, OPS and HOF rate at every level. |

**What makes it dynamic:** a field parameter driving the leaderboard, measure-driven titles that rewrite themselves with the selection (e.g. *"TOP HITTERS BY HOME RUNS"*, *"HALL OF FAMERS HIT 3.x THE HOME RUNS OF THE FIELD"*), drill-through from any player mark to the profile page, drill-down through the Explorer matrix, and `RANKX`-based percentile/rank measures.

**Custom SVG visuals (DAX `ImageUrl` measures, no custom-visual imports):** gold-star HOF badges and faint dots in tables; Cubby-blue data bars with the leader in red; the Chicago Cubs "C" roundel for legends; colour-coded archetype pills; and a six-stat percentile panel that turns a bar red above the 90th percentile. All are DAX measures returning inline SVG — they render in native table, matrix, and image visuals.

---

## The measure library

66 measures on a dedicated `_Measures` table, organised into display folders (Counting, Rate Stats, Power, Discipline, Speed, Value, Hall of Fame, Profile, Dynamic, and SVG visuals). Highlights:

- **Rate stats done right.** Batting average, OBP, slugging, OPS, ISO are all computed as `DIVIDE(SUM(num), SUM(denom))` — never an average of per-row ratios. That's the difference between a number that's correct at any grain (one player, a band, the whole league) and one that quietly mis-weights as soon as you group.
- **Sabermetrics from raw totals:** Total Bases, Extra-Base Hits, Runs Created (Bill James basic), Power-Speed Number, BB/K, SB success %, AB/HR, per-162 rates.
- **A dynamic engine:** a "Rank By" field parameter, a `Selected Metric Value` switch (with a dynamic format string so counts and rates each format correctly), and `RANKX` measures for rank and percentile.

Twelve calculated columns add per-player sabermetrics, hit/OPS bands, a power-vs-contact archetype, and a Cubs-legend flag.

The model was validated structurally (TMDL syntax, referential integrity, acyclic calculated-column dependencies, field-parameter wiring) — 0 errors.

---

## What the data actually says

Findings computed directly from the file (real numbers, not estimates):

- **Accumulation beats power for Cooperstown.** The stat most correlated with induction is Runs Created (r ≈ +0.58), essentially tied with raw Hits and Runs. Home runs (r ≈ +0.20), RBI and ISO are far weaker; strikeouts are basically uncorrelated. Voters reward production and getting on base, not slug.
- **3,000 hits is automatic; 2,000 is a cliff.** Every player with 3,000+ hits is in (100%, n=25). The rate falls to 70% at 2,500–2,999, then collapses to 36% at 2,000–2,499 and 10% below. OPS shows the same threshold shape (79% above .900, 18% in the .700s).
- **The compilers.** Omar Vizquel — 2,877 hits, 24 years, but a .688 OPS (lowest of any 2,400-hit player) — is the archetype of the long career without the plaque.
- **The snubs.** Among non-inducted players with .900+ OPS: Todd Helton (.954), Lance Berkman, Shoeless Joe Jackson (.934, banned), Albert Belle — elite rates, short or controversial careers.
- **Rickey Henderson is a one-man outlier.** 1,406 stolen bases — 50% clear of second place (Lou Brock, 938), the largest #1-to-#2 gap of any stat in the file.

---

## Design system

Custom **Chicago Cubs** theme (`baseball_light.Report/StaticResources/RegisteredResources/Chicago Cubs.json`), built from the tokens in `../../design_systems/chicago_cubs/`:

- **Colour:** Cubby Blue `#0E3386` (primary), Cubs Red `#CC3433` (accent), white, with supporting neutrals and field/ivy greens and gold.
- **Type:** Oswald (display, uppercase headings), Libre Franklin (body), Roboto Mono (numerics where used).
- Applied through the theme — wildcard and per-visual-type styles — so visuals inherit the look rather than carrying bespoke formatting. Header bands and the Cubs blue/white palette carry the brand.

---

## Caveats

- Sample data; career regular-season totals only. Rate stats are recomputed from raw counts.
- OBP/OPS are approximations (no HBP/SF in the source) and are labelled as such.
- Screenshots to be added after the first render in Power BI Desktop.
