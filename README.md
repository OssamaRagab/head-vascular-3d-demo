# Head Vascular Candidates — 3-D Demo

An interactive, browser-based 3-D viewer for **bright vessel candidates** extracted from a post-contrast 3-D MPRAGE MRI of the head. Built with Three.js (v0.128). No build step, no server, no patient data — just open `index.html` in a browser.

![demo screenshot placeholder](https://img.shields.io/badge/Three.js-r0.128-green) ![status](https://img.shields.io/badge/status-preliminary-orange) ![license](https://img.shields.io/badge/license-MIT-blue)

## Features

- **Threshold slider** — show more or fewer vessels by absolute gray value (range 0–5000 in original MRI units).
- **Minimum piece size slider** — hide small disconnected fragments (0–8 voxels).
- **View preset buttons** — quick axial / sagittal / coronal orientations.
- **OrbitControls** — drag to rotate, scroll to zoom.
- **Live stats panel** — vertex and face counts update as the threshold changes.
- **4 pre-computed threshold levels** baked into the page (1400, 1500, 1600, 1700, 1800, 1900, 2000, 2100, 2200, 2300, 2400, 2500, 2600, 2700, 2800), each with its own compressed mesh geometry.
- **Dark / light theme** support via `prefers-color-scheme`.
- **Coordinate grid** — axes are in millimeters (grid squares = 10 mm).

## Quick start

```bash
git clone https://github.com/OssamaRagab/head-vascular-3d-demo.git
cd head-vascular-3d-demo
# option 1: just open it
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows

# option 2: serve locally (recommended — avoids any CORS quirks)
python3 -m http.server 8000
# then visit http://localhost:8000
```

No `npm install`, no build step. The page loads Three.js from a CDN.

## How it works

The demo ships with **multiple pre-computed isosurfaces** of the bright-vessel mask, one per threshold level. Each level stores:

| Field | Meaning |
|-------|---------|
| `thr` | absolute gray-value threshold of the original MRI |
| `nv`  | number of vertices |
| `nf`  | number of faces |
| `voff`/`ioff`/`foff` | byte offsets into the compressed binary blob |
| `csz` | per-component voxel counts (for the minimum-piece-size filter) |

All geometry is packed into a single gzip-compressed base64 string (`B64`) and decoded at load time. The MATLAB pipeline that produced the mask uses `threshold = T2 + bias` and `minComponentVoxels` for the minimum piece size — these map directly onto the two sliders in the UI.

## Controls

| Action | Input |
|--------|-------|
| Rotate | left-drag |
| Pan    | right-drag or shift+left-drag |
| Zoom   | scroll wheel |
| Reset view | view preset buttons |

## File layout

```
head-vascular-3d-demo/
├── index.html      # the entire app (HTML + CSS + JS + embedded mesh data)
├── README.md       # this file
├── LICENSE         # MIT
└── .gitignore
```

The HTML file is ~13 MB because the mesh geometry is embedded inline. If you want a smaller repo, you can split `B64` out into a separate `.bin.gz` file and `fetch()` it at runtime — see the inline comments in `index.html`.

## Important disclaimers

> **Preliminary and intensity-based.** Other bright tissue can be included, and the segmentation has **not been validated**. **Not for clinical use.**
>
> Axes are in millimeters (grid squares = 10 mm).
> Contains **no DICOM images or patient information**.
> Thresholds are absolute gray values of the original MRI (range 0 to 5000).

## Tech stack

- [Three.js](https://threejs.org/) r0.128 (loaded from CDN)
- [OrbitControls](https://threejs.org/docs/#examples/en/controls/OrbitControls) from `three@0.128.0/examples/js/controls/OrbitControls.js`
- Plain CSS custom properties for theming
- No framework, no bundler, no dependencies beyond Three.js

## License

MIT — see [LICENSE](./LICENSE).
