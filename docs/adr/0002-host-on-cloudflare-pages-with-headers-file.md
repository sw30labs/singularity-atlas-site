# 0002. Host on Cloudflare Pages, with security headers set in `_headers`

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The static output needs a public host with HTTPS, custom response headers, and per-path caching. GitHub Pages is not enabled on this repo (`has_pages: false`).

## Decision

The site is served by **Cloudflare Pages**, which builds from this repo. Headers are declared in a Cloudflare-format `_headers` file at the root:

- `X-Content-Type-Options: nosniff`, `X-Frame-Options: DENY`, `Referrer-Policy: strict-origin-when-cross-origin`, and a `Permissions-Policy` that disables camera, microphone and geolocation.
- A strict `Content-Security-Policy`. The only third-party origins it allows are Cloudflare Web Analytics (`static.cloudflareinsights.com`, `cloudflareinsights.com`), added in `ebef557`.
- `404.html` provides a custom not-found page (added in `ebef557`).
- `robots.txt` allows all crawlers.

## Consequences

Positive
- Hardening headers and the CSP are version-controlled alongside the content.
- Cloudflare Web Analytics is the only external script the CSP permits.

Negative
- The host is tied to Cloudflare's `_headers` syntax, including the `/:day/` placeholder (ADR 0005).
- Every pushed branch can get a Cloudflare Pages preview deployment, and the commit check reports a "Cloudflare Pages" run. A `preview` branch exists for this purpose (A-009).

## Alternatives considered

GitHub Pages: not enabled, and its reasons for not being used aren't recorded (A-001).

## Evidence

- `_headers`, `404.html`, `robots.txt`
- Commits `1c78bd8` (first `_headers`), `ebef557` (adds Cloudflare Insights to the CSP and adds `404.html`), `30d245c` (current CSP)
- GitHub check-run on `08ab792`: app `cloudflare-workers-and-pages`, name "Cloudflare Pages", conclusion success
- Branch `preview` (`2d68528`)
