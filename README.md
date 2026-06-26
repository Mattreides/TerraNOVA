# TERRANOVA

A four-panel geospatial intelligence dashboard — live aircraft, weather radar,
satellite imagery, and geomagnetic activity — in a single self-contained HTML file.
No build step, no backend, no API keys. A dark ops-center interface that runs in any
modern browser.

## Panels

- **CH-01 · ADS-B** — Live aircraft within a configurable radius (default 250 nm
  around East Tennessee), rendered as heading-oriented contacts with callsign,
  altitude, speed, and bearing tooltips. Source: airplanes.live. Refreshes every 30s.
- **CH-02 · Doppler** — Global precipitation radar with a RADAR/INFRARED toggle and an
  ANIMATE control that loops the last ~13 frames to show storm movement. Source:
  RainViewer. Refreshes every 5 min.
- **CH-03 · Satellite** — ESRI World Imagery (down to ~15 cm in populated areas) with a
  transparent boundaries/roads/labels reference overlay.
- **CH-04 · Geomagnetic** — Planetary K-index with a Kp scale strip and a Kp-derived
  auroral-oval projection over both poles. Source: NOAA SWPC. Refreshes every 5 min.

A **SYNC VIEWS** toggle in the footer locks all four panels to a shared pan/zoom.

## Running it

Because the page makes cross-origin `fetch()` calls, browsers block them from a bare
`file://` open. Serve it over a local HTTP server instead — any one of:

```bash
# Python (built in)
python3 -m http.server 8000
# then open http://localhost:8000

# or Node
npx serve .
```

Or host it for free on **GitHub Pages**: push the repo, then Settings → Pages → deploy
from `main` / root. The file is named `index.html` so Pages serves it at the root URL
with zero config — giving you a shareable live demo link.

## A note on the ADS-B source (the interesting part)

The first build used OpenSky's global `states/all` feed. It works — but OpenSky returns
`Access-Control-Allow-Origin: https://opensky-network.org`, so a browser **blocks** the
request from any other origin even though the data is public. The fix was to source ADS-B
from **airplanes.live**, which returns `Access-Control-Allow-Origin: *` and so works from
any origin with no proxy. The trade-off: airplanes.live is queried by radius around a
point rather than globally — which, for a location-centered dashboard, actually produces a
denser and more readable picture. RainViewer and NOAA SWPC both send `*` already.

If you want **global** coverage back, OpenSky can be restored by running a ~30-line local
proxy that re-serves its endpoint same-origin (same pattern as the companion proxy in the
TERRANOVA wallpaper project). The static airplanes.live path is the default because it
needs no backend.

## Configuration

Edit the constants near the top of the `<script>` block in `index.html`:

| Constant         | Default            | Purpose                                      |
| ---------------- | ------------------ | -------------------------------------------- |
| `HOME`           | `[35.96, -83.92]`  | Default map center (lat, lon)                |
| `ADSB_CENTER`    | `HOME`             | Center of the ADS-B query                    |
| `ADSB_RADIUS_NM` | `250`              | ADS-B search radius in nautical miles (≤250) |

## Data sources

airplanes.live (ADS-B) · RainViewer (radar) · ESRI ArcGIS (imagery) ·
NOAA SWPC (planetary K-index) · Leaflet (map engine, via unpkg CDN)

All are free, public, key-less endpoints. Everything runs client-side; nothing is logged
or proxied.

## License

MIT — see [LICENSE](LICENSE).
