# ASC Background Generator

A single-page wallpaper generator for **Aalto Scientific Computing (ASC)**. Open it in a browser and reload to get a new background each time.

**Live demo:** https://eglerean.github.io/asc_background_generator/

## What it generates

Each reload produces a unique combination of:

- **Sunset gradient** — dark indigo night sky at the top fading through twilight blue and dusky purple into a warm horizon glow at the bottom (~18% of the height), like a sky a few minutes after sunset.
- **Honeycomb clusters** — 3–7 sparse clusters of small hexagons scattered across the canvas, semi-transparent over the gradient.
- **ASC logo** — three larger hexagons (2× the cluster size) arranged with **A** on top, **S** bottom-left, **C** bottom-right, always centered on the canvas. Letters use the Inter font.

All graphics are SVG vectors, so the output is crisp at any resolution.

## Usage

Open `index.html` directly in a browser — no server or build step needed.

**To get a new pattern:** reload the page (`⌘R` / `F5`).

**To save as a wallpaper:** use the download bar at the bottom of the page.

## Download sizes

| Button | Resolution | Fits |
|---|---|---|
| 1920×1080 | FHD | Most monitors |
| 2560×1440 | QHD / 2K | 27" monitors |
| 3840×2160 | 4K UHD | 4K monitors |
| 2560×1600 | — | MacBook Air / MacBook Pro 13" |
| 3024×1964 | — | MacBook Pro 14" |
| 3456×2234 | — | MacBook Pro 16" |
| 5120×2880 | 5K | iMac / Pro Display XDR |

Downloads are PNG files. Non-16:9 sizes crop the sides symmetrically (same as `background-size: cover` on a desktop).

> **Note:** the first download on a fresh page load may take a moment while the font is embedded into the export. Subsequent downloads are instant.

## Files

```
index.html              — the entire application (SVG + JS + download UI)
asc-socialshare-02.webp — logo reference image (design source)
CLAUDE.md               — notes for Claude Code (AI assistant context)
```

## Customisation

All parameters are in `index.html`. See [`CLAUDE.md`](CLAUDE.md) for a full reference. Quick pointers:

- **Colours** → `BASE_STOPS` array in `buildGradient()`
- **Cluster density** → `randInt(3, 7)` and `randInt(1, 3)` in `buildClusters()`
- **New export size** → add a `<button onclick="downloadWallpaper(W,H,this)">` in the download panel

## About ASC

[Aalto Scientific Computing](https://scicomp.aalto.fi) provides data, software, computing, HPC, and training services for researchers at Aalto University.
