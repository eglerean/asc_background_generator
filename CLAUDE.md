# CLAUDE.md — ASC Background Generator

## Project overview

Single-file (`index.html`) SVG-based desktop wallpaper generator for Aalto Scientific Computing (ASC). Every page reload produces a unique background. No build step, no bundler, no dependencies beyond a Google Fonts link.

## Architecture

Everything lives in `index.html`:
- **SVG canvas** — `viewBox="0 0 1920 1080"`, `preserveAspectRatio="xMidYMid slice"` so it fills any screen at any aspect ratio without letterboxing.
- **JS IIFE** — generates the SVG content programmatically on each load.
- **Download panel** — floating UI outside the SVG; handles font preloading and PNG export at multiple resolutions.

## Key design parameters

| Constant | Location | Purpose |
|---|---|---|
| `BASE_STOPS` | `buildGradient()` | 5 HSL gradient stops (top→bottom = dark indigo→warm horizon) |
| `R_SMALL = 35` | `buildClusters()` | Circumradius of honeycomb cluster hexagons |
| `R = 70` | `buildLogo()` | Circumradius of ASC logo hexagons (must stay 2× R_SMALL) |
| `LOGO_CLEARANCE = 280` | `buildClusters()` | Min distance (px, viewBox units) from logo center before placing a cluster |
| `numClusters = randInt(3,7)` | `buildClusters()` | Number of random honeycomb clusters per load |
| `axialRadius = randInt(1,3)` | `buildClusters()` | Rings of hexagons per cluster (1=7 hexes, 2=19, 3=37) |
| Base gradient angle `10°` | `buildGradient()` | Near-vertical top→bottom; jittered ±20° each reload |
| Hue shift `±15°` | `buildGradient()` | Applied uniformly to all stops for per-reload colour variation |

## Hex geometry

All hexagons are **pointy-top** (one vertex pointing straight up). Key formulas:

```
// Path vertices — angles in degrees
[-90, -30, 30, 90, 150, 210]

// Axial grid → pixel (pointy-top)
x = cx + R * (√3·q  +  √3/2·r)
y = cy + R * (3/2·r)

// Hexes within axial radius N: max(|q|, |r|, |q+r|) ≤ N

// ASC logo hex centres (R = logo circumradius)
A: (cx,          cy)
S: (cx − √3R/2,  cy + 3R/2)
C: (cx + √3R/2,  cy + 3R/2)
// All three pairs share an edge; centre-to-centre distance = √3·R ✓
```

## Gradient colour palette

The gradient runs **top (dark) → bottom (warm)**, mimicking a post-sunset sky:

| Stop | Position | Base HSL | Description |
|---|---|---|---|
| 0 | 0% | hsl(238, 60%, 13%) | Near-night deep indigo |
| 1 | 38% | hsl(228, 52%, 22%) | Dark twilight blue |
| 2 | 63% | hsl(318, 28%, 30%) | Dusky purple transition |
| 3 | 82% | hsl(18, 62%, 40%) | Muted sunset orange |
| 4 | 100% | hsl(25, 75%, 48%) | Dim horizon glow |

Stop at index 2 (`i === 2`) is used to derive the ASC letter colour (darkened by −40 lightness).

## Download / PNG export

Font embedding is required for correct canvas rasterisation — external font URLs are blocked when an SVG is drawn via `drawImage`. On page load, `preloadFont()` fetches the Inter Bold woff2 binary from Google Fonts and stores it as a base64 data URI in `fontDataURI`. This is injected into the SVG clone's `<defs>` before rasterisation.

Supported export sizes (all PNG):

| Label | Size | Aspect |
|---|---|---|
| 1920×1080 | FHD | 16:9 |
| 2560×1440 | QHD | 16:9 |
| 3840×2160 | 4K UHD | 16:9 |
| 2560×1600 | MacBook Air/13" | 16:10 |
| 3024×1964 | MacBook Pro 14" | ~16:10 |
| 3456×2234 | MacBook Pro 16" | ~16:10 |
| 5120×2880 | 5K iMac | 16:9 |

Non-16:9 exports use `xMidYMid slice` — the content scales to fill the target canvas, cropping the sides. This is the correct wallpaper behaviour.

## Common modifications

**Change the colour palette** — edit `BASE_STOPS` in `buildGradient()`. Use HSL. The warm zone should stay in the lower positions (≥ 0.7) to preserve the sunset feel.

**More/fewer clusters** — change the arguments to `randInt(3, 7)` in `buildClusters()`.

**Larger clusters** — change the arguments to `randInt(1, 3)` (axial rings). `4` gives 61 hexes — avoid on slow machines.

**Cluster opacity** — adjust the `rand(0.08, 0.18)` (fill) and `rand(0.25, 0.45)` (stroke) ranges in `buildCluster()`.

**Logo position** — `cx = 960`, `cy = 540` in `buildLogo()`. Add `± rand()` offsets for per-reload jitter.

**Add more export sizes** — add a `<button onclick="downloadWallpaper(W,H,this)">` in the download panel HTML. No JS changes needed.

## Live demo

Published on GitHub Pages: <https://eglerean.github.io/asc_background_generator/>
Source: branch `main`. Pages: branch `gh-pages`.
