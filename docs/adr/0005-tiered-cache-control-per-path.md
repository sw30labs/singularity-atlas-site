# 0005. Set `Cache-Control` per path, by how often each path changes

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The site mixes content that never changes (vendored libraries), content refreshed during the day (baked data, the latest snapshot), and per-day archives.

## Decision

`_headers` sets:

| Path | Cache-Control |
|---|---|
| `/vendor/*` | `public, max-age=31536000, immutable` |
| `/data/*` | `public, max-age=300` |
| `/snapshot.json` | `public, max-age=300` |
| `/:day/snapshot.json` | `public, max-age=86400` |

Dated snapshots started as `max-age=31536000, immutable` (`1c78bd8`). They were lowered to `max-age=86400` in `4fc7a9f`, "Dated snapshot cache: max-age=86400". *Inferred why:* dated snapshots do get rewritten in place. The source publisher scrubs `llm` and `brief.model` from older dated snapshots, and the backfills on 2026-10-16 rewrote them, so a 1-year immutable cache would pin stale or unscrubbed copies (A-003).

## Consequences

Positive
- Vendor assets are cached for good. Their content is pinned by hash in the builder (ADR 0006).
- The latest data reaches visitors within 5 minutes of a publish.

Negative
- `/:day/` matches any single path segment, not just dates. No other `*/snapshot.json` exists today.
- HTML, `app.js` and `style.css` have no explicit rule and rely on Cloudflare defaults. `app.js` and `style.css` use `?v=` cache-busting.

## Alternatives considered

Immutable dated snapshots, used from `1c78bd8` until `4fc7a9f`.

## Evidence

- `_headers`
- Commits `1c78bd8`, `4fc7a9f`, `30d245c`
- External: `scripts/publish_public_site.sh` scrub step ("Dated snapshots published before 2026-10-16 carried 'llm'…")
