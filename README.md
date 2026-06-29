# Power BI Sample Work

A public portfolio of Power BI work — data models, semantic layers, and reports — built by a practitioner, not a vendor.

I'm Robert McFarlin, CFO of RCM Industries (aluminum die casting, multiple divisions). I build our reporting and financial models myself in Power BI and Excel rather than outsourcing the analytics. This repository is where I show the work: real models, real reports, the kind of thing a mid-market manufacturing finance or ops leader actually has to build.

Everything here is sanitized — structure and technique are real, the underlying data is anonymized or synthetic. Demonstrated, not claimed.

---

## Navigation

| Section | What's in it |
|---|---|
| [Data](./data/) | Source data files — CSVs, Excel extracts, raw inputs that feed the models |
| [Datasets](./datasets/) | Semantic models / data models — relationships, measures, the reusable layer |
| [Reports](./reports/) | Power BI reports — `.pbip`/`.pbix` projects plus screenshots |
| [Design systems](./design_systems/) | Reusable brand design systems applied to reports (e.g. the Ballpark navy/crimson palette) |
| [Assets](./assets/) | Images used in this README (covers, thumbnails) |

---

## Projects

> Index of complete pieces of work. Each entry links to the report and notes the model and data behind it.

| Project | Report | Data | Highlights |
|---|---|---|---|
| **Career Hitting Leaders** | [baseball_light](./reports/baseball_light/) | [baseball_hits.csv](./data/baseball_hits.csv) | 465 hitters; 71-measure sabermetrics library, dynamic field-parameter leaderboard, Hall-of-Fame analysis, drill-through player profiles, SVG KPI tiles & icons — styled with a custom [Ballpark](./design_systems/ballpark/) navy/crimson design system. [Read the writeup →](./reports/baseball_light/README.md) |
| **Baseball API** _(in progress)_ | [baseball_api](./reports/baseball_api/) | live [MLB Stats API](https://statsapi.mlb.com/api/v1/) | Built directly on a live REST API via Power Query (`Web.Contents` → JSON) into a refreshable model — standings + hitting/pitching leaderboards on the Ballpark design system. [Read the plan →](./reports/baseball_api/README.md) |

---

## How this is organized

- **By artifact type at the top level** — `data/`, `datasets/`, `reports/` — because the same model often feeds more than one report and the same source feeds more than one model.
- **By project inside each folder** — e.g. `reports/margin-analysis/`, `datasets/manufacturing-finance/`. A project's pieces share a name across folders so they're easy to trace.
- **PBIP format preferred** — reports and models are committed as Power BI Project (`.pbip`) folders where possible, so the TMDL and report definitions are diff-able and reviewable. `.pbix` files are included when a project predates that.

## Conventions

- Sample data is anonymized or synthetic. Nothing here exposes real company figures.
- Each project folder carries a short `README.md` describing the problem, the approach, and what to look at.
- Screenshots live alongside the report they document.

---

*Practitioner to practitioner. If something here is useful, take it.*
