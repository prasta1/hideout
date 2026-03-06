# hideout

A single-page web app that scans terrain in the Northeast US and scores locations by natural concealment — hollows, forest canopy, and rugged terrain. Built as a fun side project.

## What it does

The app fetches real geographic data for a selected region, generates a grid of candidate points, scores each one for natural enclosure, and plots the top 10 on an interactive map.

**Scoring factors (weighted composite out of 100):**
- **Concavity (45%)** — how sunken/hollow the terrain is relative to its neighbors. Fetched from OpenTopoData (SRTM 30m elevation data). Each candidate point is compared against 8 surrounding neighbor points ~300m away.
- **Canopy coverage (35%)** — whether the point falls inside a forest or woodland polygon. Fetched from OpenStreetMap via the Overpass API (`landuse=forest`, `natural=wood`).
- **Ruggedness (20%)** — elevation spread across the 8 neighbor points. High spread = broken terrain = harder to observe.

## Regions covered

- Adirondacks, NY
- Catskills, NY
- Green Mountains, VT
- Western Maine Highlands
- Berkshires, MA

## Tech stack

- **Leaflet.js** — map rendering and interaction (tiles from OpenStreetMap)
- **OpenTopoData API** — free SRTM elevation data, no key required
- **Overpass API** — free OpenStreetMap query endpoint, no key required
- Vanilla HTML/CSS/JS, single file, no build step

## File structure

```
hideout/
├── README.md
└── index.html            # entire app lives here
```

## How to run locally

Open `index.html` directly in a desktop browser — it works fine there. For mobile, serve it over HTTP so the API calls aren't blocked by CORS:

```bash
python3 -m http.server 8080
```

Then open `http://YOUR_LOCAL_IP:8080/` on your phone. Both devices must be on the same WiFi network.

## Hosted version

Deployed via GitHub Pages at:
https://prasta1.github.io/hideout/

## UI features

- Auto-scans the default region (Adirondacks) on page load
- Collapsible sidebar for full-screen map on mobile (chevron toggle tab)
- Clicking a marker or sidebar card zooms to level 14 and highlights the card
- Clicking the map background zooms back out to the full region overview
- Scan button re-runs the analysis for any selected region

## Git workflow

After any change:

```bash
git add index.html
git commit -m "describe what changed"
git push
```

GitHub Pages redeploys automatically within ~30 seconds.

## Known limitations

- CORS blocks API calls when opened as a `file://` URL on mobile — use the local server or hosted URL
- OpenTopoData has a rate limit; the app batches requests in chunks of 100 points with a 300ms delay between chunks
- Overpass API can be slow or occasionally unavailable; the app falls back to terrain-only scoring if it fails
- Candidate points are stratified-random, so results vary slightly between scans of the same region
- Forest polygon detection uses a ray-casting point-in-polygon algorithm — accurate for convex polygons, may have edge cases on complex shapes

## Potential next steps

- Tune the scoring weights via a UI slider (concavity/canopy/ruggedness)
- Add viewshed analysis (line-of-sight visibility from roads/trails)
- Integrate NLCD (National Land Cover Database) for higher-resolution canopy data
- Add more regions beyond the Northeast
- AI-generated plain-English description per site (was built, removed to avoid API costs — consider Groq free tier or local Ollama when ready)
- Export candidate sites as GPX for use in mapping apps
