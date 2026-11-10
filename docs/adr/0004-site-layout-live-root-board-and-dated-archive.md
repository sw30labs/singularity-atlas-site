# 0004. Site layout: live atlas at `/`, today's board at `/board/`, dated archive at `/YYYY-MM-DD/`

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The site publishes three kinds of content: the interactive atlas (ADR 0003), a daily screenshot board with the brief, and a history of past days.

## Decision

The site tree is fixed:

| Path | Content |
|---|---|
| `/` | Interactive atlas (`index.html`, `app.js`, `style.css`, `static-shim.js`, `favicon.svg`) |
| `/data/…`, `/vendor/…` | Baked API JSON. Pinned globe.gl and the earth texture |
| `/board/` | Today's board (`index.html`, `board.png`) |
| `/YYYY-MM-DD/` | One folder per day: `index.html`, `snapshot.json`, `board.png` |
| `/archive/` | Index of the dated folders, newest first, with each day's SI |
| `/snapshot.json` | Latest slim snapshot (monitoring) |

- Until `30d245c`, the board lived at `/` (root `index.html` and `board.png`). It moved to `/board/` when the live atlas took the root.
- Dated folders for 2026-10-09, 10-10, 10-12 and 10-14 were backfilled on 2026-10-16 (`1c78bd8`, `283afb1`, `44c2dc7`, `ed897ae`). 10-11, 10-13 and 10-15 are missing.
- Since then, one dated folder has been added per day. `/board/` is identical to that day's dated folder.

## Consequences

Positive
- Stable, guessable URLs for each day. The archive page is regenerated from the folders present.
- `/snapshot.json` is a small machine-readable heartbeat.

Negative
- Each day adds a ~0.55 MB PNG to git history. `board/board.png` is byte-identical to the dated copy, so git stores it once. That's roughly 200 MB a year of binary history (A-010).
- Gaps in the archive (missing days) are silent.
- Older dated pages (2026-10-09 to 10-14) keep the earlier footer, which says "local live is `http://localhost:8055`", and the older board style.

## Alternatives considered

Board-only layout with the board at `/`. This is still the publisher's fallback when the live build is absent. *From the external script; not visible in this repo.*

## Evidence

- Tree at `08ab792`. `cmp` shows `board/index.html`, `board/board.png` and `snapshot.json` are identical to `2026-10-25/…`
- `archive/index.html`, `404.html` nav ("Live atlas / Today's board / Archive")
- Commits `1c78bd8`, `283afb1`, `44c2dc7`, `ed897ae`, `7051704` (backfill/first day), `30d245c` (board moved to `/board/`)
- `2026-10-09/index.html` line 86 (localhost footer)
