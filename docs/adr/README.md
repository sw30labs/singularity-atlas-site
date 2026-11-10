# Architecture Decision Records

These are retrospective ADRs for `sw30labs/singularity-atlas-site`, the publish-only static site for The Singularity Atlas. They were reconstructed on 2026-10-25 from the repo tree at `08ab792`, its full git history, and the publisher scripts in `sw30labs/singularity-atlas`. Where a rationale is inferred rather than recorded, the ADR says so and the inference is logged in [../assumptions.md](../assumptions.md).

| # | Title | Status |
|---|---|---|
| [0001](0001-publish-only-site-repo-pushed-from-the-mac.md) | Publish-only site repo, pushed one-way from the Mac | Accepted (retrospective, inferred 2026-10-25) |
| [0002](0002-host-on-cloudflare-pages-with-headers-file.md) | Host on Cloudflare Pages, with security headers set in `_headers` | Accepted (retrospective, inferred 2026-10-25) |
| [0003](0003-ship-the-live-dashboard-as-a-backend-free-static-app.md) | Ship the live dashboard as a backend-free static app, using a fetch shim | Accepted (retrospective, inferred 2026-10-25) |
| [0004](0004-site-layout-live-root-board-and-dated-archive.md) | Site layout: live atlas at `/`, today's board at `/board/`, dated archive at `/YYYY-MM-DD/` | Accepted (retrospective, inferred 2026-10-25) |
| [0005](0005-tiered-cache-control-per-path.md) | Set `Cache-Control` per path, by how often each path changes | Accepted (retrospective, inferred 2026-10-25) |
| [0006](0006-self-host-and-pin-all-third-party-assets.md) | Self-host and pin all third-party assets; no web fonts or CDNs | Accepted (retrospective, inferred 2026-10-25) |
| [0007](0007-publish-sanitised-data-only.md) | Publish only sanitised data: no corpus, no model names, no raw errors | Accepted (retrospective, inferred 2026-10-25) |
| [0008](0008-daily-automated-publish-commit.md) | One automated publish commit per day at about 07:00 ET, directly to `main` | Accepted (retrospective, inferred 2026-10-25) |

Format: Title · Status · Context · Decision · Consequences (Positive / Negative) · Alternatives considered · Evidence.
