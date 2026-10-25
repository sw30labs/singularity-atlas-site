# 0008. One automated publish commit per day at about 07:00 ET, directly to `main`

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The board is a "24h public snapshot" that is "replaced at least daily" (`board/index.html` lede).

## Decision

- An automated publisher commits the whole regenerated tree and pushes straight to `main`, which Cloudflare Pages deploys.
- Since 2026-10-17 the commits have landed daily between 07:00:16 and 07:01:08 -0400 (`707c228` … `08ab792`).
- On 2026-10-16 there were several manual-looking runs between 08:10 and 10:08 while the layout changed.
- `snapshot.json.generated_et` records the capture time (for example `2026-10-25 07:00 EDT`).
- `main` has no branch protection.
- A separate `preview` branch (2 ahead, 12 behind `main`) was used for the live-atlas trial on 2026-10-16 (`76b4a4f`, `2d68528`).

## Consequences

Positive
- Fully hands-off. A failed capture leaves the previous day's site in place.

Negative
- No review gate. Whatever passes the publisher's own scans goes live.
- If the Mac is asleep or off at 07:00, no snapshot is produced that day. Missing days aren't backfilled automatically (10-11, 10-13 and 10-15 are absent).
- The commit history is machine noise. Design changes ride inside "Publish 24h public snapshot" commits (for example `30d245c`), with no descriptive message.

## Alternatives considered

None documented.

## Evidence

- `git log --format='%h %ad %an'` (commit times and authorship)
- `snapshot.json` `generated_utc` and `generated_et`
- GitHub API: `branches/main/protection` → 404 "Branch not protected"; `compare/main...preview` → ahead 2 / behind 12
- External: `scripts/publish_public_site.sh` ("Never prompt under launchd", `ATLAS_SITE_BRANCH`)
