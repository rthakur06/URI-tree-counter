# URI Global Tree Counter

A single-page microsite for the United Religions Initiative's Earth Restoration program. It reads tree-planting submissions from a Google Form's response sheet and shows the network's collective impact: a live total, a world map by country, environmental-impact estimates, a rotating Cooperation Circle spotlight, and a photo wall of the work on the ground.

The whole site is one self-contained `index.html` — no build step, no server, no backend.

## Features

- **Live total** of trees planted (Jan 1 – Jun 30, 2026), counting up on load and re-checking the sheet every 45 seconds.
- **World map by country** (choropleth + markers). Tap a country to see every Cooperation Circle / member organization that planted there, with descriptions and photos.
- **Network stats**: countries, Cooperation Circles, and plantings logged.
- **Estimated environmental impact**: CO₂ absorbed per year and a cars-off-the-road equivalent, clearly labeled as estimates.
- **Rotating spotlight** that features a random CC every 30 seconds.
- **Photo mosaic** gathering every shared image into a tappable wall with captions.
- **Submission CTA** linking to the Google Form, with an email fallback to Lauren.

## How it works

```
Google Form  ──▶  Responses sheet  ──▶  index.html (reads the sheet as CSV, renders everything)
```

The page fetches the sheet through Google's public CSV endpoint (`gviz/tq?tqx=out:csv`), so there are no API keys and nothing to maintain server-side. It re-fetches on an interval, so new submissions appear automatically.

## Configuration

All settings live in the `CONFIG` block at the top of `index.html`:

```javascript
const CONFIG = {
  SHEET_ID: "1Ukv0JtHRkhgQ4QP8l-PBAy1Op7vtgp3DG9bdcghnFN8", // the sheet's ID
  SHEET_NAME: "Form_Responses",        // the tab to read
  START_DATE: "2026-01-01",            // counting window (inclusive)
  END_DATE:   "2026-06-30",
  REFRESH_SECONDS: 45,                 // how often to re-check the sheet
  ADD_NUMBERS_URL: "https://docs.google.com/forms/d/e/.../viewform", // submission form
  CO2_KG_PER_TREE_YEAR: 10,            // impact estimate (conservative)
  CAR_KG_CO2_YEAR: 4600                // ~CO₂ from one average car per year (EPA)
};
```

**The Sheet ID** is the long string in the sheet URL, between `/d/` and the next `/`:
`docs.google.com/spreadsheets/d/`**`THIS_PART`**`/edit`

## Google Sheet setup

| Field | What it is | Matches headers like |
|-------|------------|----------------------|
| Country | Required. Groups plantings on the map. | "Country" |
| CC/IM | Cooperation Circle or member org name | "Name of your CC…", "CC", "group" |
| Trees | Number planted | "Number of trees…", "Trees" |
| Location | City or region (display only) | "City or Region", "Location" |
| Description | Short note about the project | "describe", "notes", "about" |
| Photos | Image link(s) | "Photos", or any Drive/image link in any column |
| Date | Optional planting date | "Date" (if absent, all rows count) |

Country names are forgiving: "USA", "United States", "U.S." all map to the same country; likewise UK, UAE, DR Congo, Côte d'Ivoire, etc.

## Photos (important)

Photos are read from Google Drive links anywhere in a row (e.g. `drive.google.com/file/d/<id>/view`). **Drive files are private by default**, so the page can only display them if the file is publicly viewable.

- If the form collects photos via a **file-upload question**, the uploads land in a Drive folder owned by the form's creator.

Images that can't load are hidden automatically rather than showing broken icons.

## Local preview

```bash
# from the project folder
python3 -m http.server 8000
# then open http://localhost:8000
```

## Customizing

- **Colors / branding**: CSS variables at the top of the `<style>` block (`--ink`, `--blue`, `--teal`, `--green`, `--gold`). The URI logo is loaded live from uri.org.
- **Spotlight speed**: the `30000` (ms) value in `startSpotlight()`.
- **Impact factors**: `CO2_KG_PER_TREE_YEAR` and `CAR_KG_CO2_YEAR` in `CONFIG`.
- **Counting window**: `START_DATE` / `END_DATE`.

## Built with

Plain HTML/CSS/JavaScript, plus three libraries loaded from a CDN (no install needed): [D3](https://d3js.org/) and [topojson-client](https://github.com/topojson/topojson-client) for the map, and [PapaParse](https://www.papaparse.com/) for reading the sheet CSV. World boundaries from [world-atlas](https://github.com/topojson/world-atlas).

---

A project of the **United Religions Initiative** · Earth Restoration.
