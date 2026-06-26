# ShadowWalk ☀️

A walking route planner that picks the shadiest path based on real building shadow projections at any time of day.

![ShadowWalk screenshot](screenshot.png)

## How it works

1. Set a start and end point on the map
2. Choose the time you plan to walk
3. ShadowWalk fetches real building data, calculates shadow polygons, scores each route alternative by shade coverage, and highlights the shadiest path

### Shadow calculation

- **Sun position** — computed via [SunCalc](https://github.com/mourner/suncalc) from latitude, longitude, and time
- **Building data** — fetched live from [OpenStreetMap](https://www.openstreetmap.org/) via the [Overpass API](https://overpass-api.de/), including `building:levels` tags for height estimation
- **Shadow geometry** — each building footprint is projected in the direction opposite the sun:
  ```
  shadow_length = building_height / tan(sun_altitude)
  shadow_direction = sun_azimuth + 180°
  ```
  The shadow polygon is the convex hull of the building footprint + its projected vertices.
- **Route scoring** — each route is sampled every 5 metres; a point scores as "in shade" if it falls inside any shadow polygon. The route with the highest shade percentage wins.

## Usage

No install or API key needed — it's a single static HTML file.

```bash
# Clone and open
git clone <repo-url>
open shadow-walk/index.html
```

Or just open `index.html` directly in any modern browser.

## Tech stack

| Library | Purpose |
|---|---|
| [Leaflet](https://leafletjs.com/) | Interactive map |
| [SunCalc](https://github.com/mourner/suncalc) | Sun azimuth & altitude |
| [Turf.js](https://turfjs.org/) | Geospatial geometry (convex hull, point-in-polygon) |
| [Overpass API](https://overpass-api.de/) | OpenStreetMap building footprints & heights |
| [OSRM](http://project-osrm.org/) | Walking route alternatives |
| [CartoDB Dark tiles](https://carto.com/basemaps/) | Dark map tiles |

## Limitations

- Shadow accuracy depends on building height data in OSM (many buildings lack height tags; falls back to 10 m)
- Works best in dense urban areas with good OSM coverage
- Sun altitude < 1° (near sunrise/sunset) is excluded to avoid near-infinite shadow lengths
- Overpass API has rate limits — very large route areas may time out

## License

MIT
