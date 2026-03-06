# hideout

A single-page web app that scans terrain in the Northeast US and scores locations by natural concealment — hollows, forest canopy, and rugged terrain. Built as a fun side project.

## What it does

The app fetches real geographic data for a selected region, generates a grid of candidate points, scores each one for natural enclosure, and plots the top 10 on an interactive map.

**Scoring factors (weighted composite out of 100):**
- **Concavity (45%)** — blended score: terrain depth relative to neighbors (60%) + directional enclosure from 8 compass bearings (40%). Fetched from OpenTopoData (SRTM 30m elevation data).
- **Canopy coverage (35%)** — gradient forest density sampling (9 points in ~150m radius) checking overlap with forest/woodland polygons from OpenStreetMap via the Overpass API.
- **Ruggedness (20%)** — elevation spread across the 8 neighbor points. High spread = broken terrain = harder to observe.

## Regions covered

**New York** — Adirondacks, Catskills, Tug Hill Plateau, Hudson Highlands

**Vermont** — Green Mountains, Northeast Kingdom

**New Hampshire** — White Mountains, North Country

**Maine** — Western Maine Highlands, Downeast & Bold Coast, North Maine Woods

**Massachusetts** — Berkshires, Central Highlands

**Connecticut** — Litchfield Hills

**Rhode Island** — Western Rhode Island

**Pennsylvania** — Pocono Mountains, Northern Tier, PA Wilds

## Tech stack

- **Leaflet.js** — map rendering and interaction (tiles from OpenStreetMap)
- **OpenTopoData API** — free SRTM elevation data, no key required
- **Overpass API** — free OpenStreetMap query endpoint for forest polygons and POI data, no key required
- Vanilla HTML/CSS/JS, single file, no build step

## File structure

```
hideout/
├── README.md
├── index.html            # entire app lives here
└── .github/
    └── workflows/
        └── deploy.yml    # GitHub Pages deployment
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

- Map starts centered on Saranac Lake, NY — scan manually when ready
- Collapsible sidebar for full-screen map on mobile (chevron toggle tab)
- Clicking a marker or sidebar card zooms to the location and highlights the card
- Clicking the map background zooms back out to the full region overview
- Score filter slider to set a minimum enclosure score threshold
- **"Run! 5-0 Comin'"** button uses GPS to find the nearest hiding spot from current scan results
- **Toggleable POI categories** (Nearby Supply panel): gun shops, grocery stores, gas stations, camping gear, boat rental, car rental — color-coded dots on the map, auto-refresh on pan/zoom

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
- POI data depends on OpenStreetMap coverage, which varies by region

## Potential next steps

- Tune the scoring weights via a UI slider (concavity/canopy/ruggedness)
- Add viewshed analysis (line-of-sight visibility from roads/trails)
- Integrate NLCD (National Land Cover Database) for higher-resolution canopy data
- Add more regions beyond the Northeast
- Export candidate sites as GPX for use in mapping apps
