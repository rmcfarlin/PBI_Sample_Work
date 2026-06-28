# Ballpark Design System

A custom **navy / crimson / gold** palette and typographic system for sports-analytics reports. Applied to Power BI through the `Ballpark` theme (`reports/*/StaticResources/RegisteredResources/Ballpark.json`) and to inline SVG visuals.

## Color

| Token | Hex | Role |
|---|---|---|
| Navy (primary) | `#0E3386` | primary brand, headings, data bars, KPI values |
| Navy 050 / 100 / 300 / 700 / 900 | `#EEF2F9` `#D6DEEE` `#4A69A8` `#0A2766` `#071C49` | tints, tracks, sequential gradient |
| Crimson (accent) | `#CC3433` | leaders, highlights, "below" / alert |
| Crimson 100 / 700 | `#F6DBDB` `#A8221F` | tints, hover |
| Gold (elite) | `#C8A24B` | Hall-of-Fame / award emphasis |
| Field green | `#3C7A3F` | positive / "contact-speed" category |
| Ivy green | `#1E4A2B` | supporting category |
| Grays 050 → 900 | `#F7F8FA` … `#12141C` | surfaces, borders, body text |

**Semantic:** good `#3C7A3F` · neutral `#C8A24B` · bad `#CC3433` · sequential gradient navy 050 → 300 → primary.

## Typography

- **Display** — Oswald (uppercase headings, KPI numbers)
- **Body** — Libre Franklin
- **Mono** — Roboto Mono (numerics where used)

Install the two Google Fonts (Oswald, Libre Franklin) for the intended look; Power BI falls back gracefully if they're absent.

## Shape & spacing

- Radius: 6px (controls), 8–10px (cards), pill 999px
- Borders: 1px hairline `#DCDFE6`; no drop shadows
- Header bands: solid navy `#0E3386`, white display type

---

*`source-mockup.html` is the original design mockup these tokens were extracted from. The palette is used here as a generic navy/crimson/gold sports theme.*
