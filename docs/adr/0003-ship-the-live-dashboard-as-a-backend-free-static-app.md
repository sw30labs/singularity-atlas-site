# 0003. Ship the live dashboard as a backend-free static app, using a fetch shim

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The local dashboard frontend (`app.js`) polls a FastAPI backend: `/api/state` every 30 s, `/api/globe` every 5 min, and `/api/ingest` every 2 s. It also calls `/api/graph`, `/api/archive/entity` and `/api/archive/search` on demand. The public site has no server. Until `30d245c` (Oct 16), the public site was only a static screenshot board.

## Decision

From `30d245c` on, the root `/` serves the **same interactive frontend** (`index.html`, `app.js`, `style.css`) in static mode:

- `static-shim.js` loads before `app.js`. It replaces `window.fetch` and maps same-origin `/api/*` GETs to baked JSON:
  - `/api/state` → `/data/state.json`
  - `/api/globe` → `/data/globe.json`
  - `/api/graph?entity=X` → `/data/graph/<slug>.json`
  - `/api/archive/entity?name=X` → `/data/archive-entity/<slug>.json`
  - Missing files fall back to empty results.
- Non-GET requests return `{"error": "read-only snapshot"}`.
- `/api/ingest` always returns `running: false`.
- `/api/archive/search` returns a single link to search on The Innermost Loop on Substack. The archive text isn't shipped.
- `data/entities.json` maps entity names to slugs.
- Asset URLs carry content-hash query strings (`style.css?v=…`, `app.js?v=…`) for cache busting.

## Consequences

Positive
- The public globe, panels, radar and entity drawers work with zero server-side code.
- One frontend codebase serves both local and public use. *Inferred:* the source builder says it "copies the unchanged frontend" (A-005).

Negative
- The data is a point-in-time bake, refreshed only when the publisher runs (daily). The 30 s and 2 s polls keep firing against static files or the shim.
- Only entities present in `data/graph/` and `data/archive-entity/` have drawer data. Others show empty results.
- Some frontend text assumes a live backend. The brief meta label maps any non-heuristic model to "qwen3 local". The ingest cat ("Aineko") never switches to watching.

## Alternatives considered

The screenshot-only board, used Oct 9–16, is still published at `/board/` (ADR 0004). No other alternatives are documented.

## Evidence

- `static-shim.js`, `index.html` (script order, `?v=` hashes), `app.js` (`pollState`, `pollGlobe`, `pollIngest`, `openEntityDrawer`, `renderBrief`)
- `data/state.json`, `data/globe.json`, `data/entities.json`, `data/graph/*.json`, `data/archive-entity/*.json`
- Commit `30d245c` ("(live atlas)": adds `app.js`, `static-shim.js`, `style.css`, `data/*`, `vendor/*`)
- External: `sw30labs/singularity-atlas` `scripts/build_live_site.py` docstring
