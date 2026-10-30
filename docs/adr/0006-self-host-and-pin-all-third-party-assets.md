# 0006. Self-host and pin all third-party assets; no web fonts or CDNs

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The first board pages loaded Google Fonts (Geist, Geist Mono, Instrument Serif). The CSP allowed `fonts.googleapis.com`, `fonts.gstatic.com` and `script-src 'unsafe-inline'`. The live atlas also needs globe.gl and an earth texture.

## Decision

From `30d245c` on:

- **Vendor** `globe.gl` 2.46.2 (`vendor/globe.gl.min.js`) and `vendor/earth-night.jpg` (4096×2048). Both are served from the site's own origin with an immutable cache. Their SHA-256 hashes match the pins in the source builder:
  - `2c3e445c…94c` (globe.gl 2.46.2)
  - `355ab23d…d24` (three-globe 2.45.6 texture)
- **Drop Google Fonts.** Use system font stacks in the board, 404 and dashboard CSS. The publisher scrubs the font `<link>` tags from older dated pages; none remain in the tree.
- **Tighten the CSP:**
  - `script-src 'self'` plus Cloudflare Insights only, with no `'unsafe-inline'`
  - `font-src 'self'`
  - `worker-src 'none'`, `object-src 'none'`, `form-action 'none'`
  - `img-src` adds `blob:` for the WebGL globe

## Consequences

Positive
- No third-party requests other than Cloudflare Analytics, and no font-provider tracking.
- With no `'unsafe-inline'` scripts, the CSP blocks `javascript:` URLs. That matters because `app.js` puts feed-supplied URLs into `href` after HTML escaping only, without checking the scheme.

Negative
- Vendored libraries need manual upgrades, and the hash pin has to be updated each time.
- `style-src 'unsafe-inline'` remains, because the board and 404 pages use inline `<style>` and `app.js` sets inline styles.

## Alternatives considered

CDN-hosted fonts and scripts, used Oct 9–16 (`1c78bd8`…`09a1214` for the board).

## Evidence

- `vendor/globe.gl.min.js` (banner "Version 2.46.2"), `vendor/earth-night.jpg`. `shasum -a 256` matches the builder pins.
- `_headers` (current) vs `git show 1c78bd8:_headers`
- `git log -S fonts.googleapis` → `1c78bd8` (added), `30d245c` (removed)
- `app.js` `esc()`, `renderVectors()`, `openStoryDrawer()`
- External: `scripts/build_live_site.py` `VENDOR` pins
