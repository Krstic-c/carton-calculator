# Carton · Pallet · Container Loading Calculator

**[中文版 →](README.md)**

A fully local, single-file, serverless carton/pallet/container loading calculator. Clean Apple-inspired UI, three linked steps covering the full loading chain from inner carton to shipping container.

> 🔒 **Privacy**: All calculations run entirely in your browser (vanilla JS). Nothing is uploaded, no server or API calls are involved, and the tool works fully offline.

---

## Project Purpose

In everyday export/factory quoting and packing-plan design, the chain "carton dimensions → outer box spec → pallet stacking → container utilization" is a frequent, repetitive calculation that's error-prone and slow to do by hand. This tool turns that chain into a visual, real-time single-page calculator: enter the source dimensions and get the recommended plan and utilization for every stage, one after another.

Reference form factor: a single HTML file that can be deployed to GitHub Pages for online use, or opened offline by double-clicking `index.html` directly — no install required.

## Tech Requirements

- **Frontend only**: HTML + CSS + vanilla JavaScript, no build step, no framework dependency
- **Zero network dependency**: loads no external fonts/scripts/assets at all; Chinese text uses the system PingFang SC font, fully usable offline
- **Deployment**: single file, deployable to GitHub Pages or usable fully offline by opening `index.html` locally
- **Compatibility**: latest Chrome / Edge / Safari

## Core Features

### STEP 01　Carton Dimensions → Recommended Outer Box
- [x] Input carton length/width/height, unit weight, quantity per box, and overall clearance margin
- [x] Automatically enumerates carton arrangements and orientations, ranking candidate outer-box options by volume utilization
- [x] Isometric drawing preview of the packing arrangement, in engineering-drawing style annotation

### STEP 02　Outer Box → Pallet Loading Utilization
- [x] Pallet size presets: 1200×1000 Asia standard / 1100×1100 Japan standard / 1067×1067 US CP1 / 1200×800 EUR standard, plus custom
- [x] Max stack height and max load weight limits (leave blank for unlimited)
- [x] Automatically calculates boxes per layer, number of layers, total box count, and volume/area utilization

### STEP 03　Pallet / Outer Box → Container Loading Utilization
- [x] Container size presets: 20GP / 40GP / 40HQ / 45HQ (internal dimensions), plus custom
- [x] Two loading modes: **by pallet** (using the pallet plan from STEP 02) / **floor loading** (no pallet, outer boxes loaded directly)
- [x] Automatically calculates loaded quantity and space utilization

### Summary Chain
- [x] The three steps' results are automatically chained together into one summary view — see the full carton-to-container loading plan at a glance

### Usability
- [x] Real-time updates: changing any input instantly refreshes downstream results and diagrams, no "calculate" button needed
- [x] Clean Apple-inspired UI (rounded cards, system font, one restrained accent color), with automatic dark mode following system preference
- [x] Pallet/container specs can be saved as reusable presets; results can be copied as text or printed/exported to PDF

## UI Layout

The three STEP cards stack top to bottom (dimensions/weight → pallet spec → container spec), each with inputs on the left and result stat cards + isometric diagram on the right; a Summary Chain at the bottom strings together the key numbers across the whole flow.

## Usage

**Offline use**: just double-click [`index.html`](index.html) to open it in your browser.

**Online (GitHub Pages)**: after enabling Pages on this repo, visit
`https://<your-github-username>.github.io/carton-calculator/`

## Possible Future Directions

- Carton rotation currently uses one heuristic orientation for the whole batch — a true per-carton mixed-orientation optimizer would pack tighter
- Export the loading plan as a full image report (PDF/print currently goes through the browser's native print)

---

More background and technical details in [CLAUDE.md](CLAUDE.md), change history in [CHANGELOG.md](CHANGELOG.md).
