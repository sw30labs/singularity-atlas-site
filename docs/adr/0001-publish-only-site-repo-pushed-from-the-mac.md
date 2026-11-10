# 0001. Publish-only site repo, pushed one-way from the Mac

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The Singularity Atlas dashboard runs locally on Pedro's Mac. Its private working repo holds the ingest pipeline, the graph, and private reference material: the Innermost Loop corpus and `ref/`. A public copy of the board was wanted without exposing that machine or that material.

## Decision

`sw30labs/singularity-atlas-site` is a **publish-only** repository. It contains only generated static output. Nothing in it is hand-edited.

- Every commit is authored by `Atlas publisher <atlas-publisher@users.noreply.github.com>`, with the message `Publish 24h public snapshot <day>[ (live atlas)]` (19 of 19 commits on `main`).
- The repo description reads "Singularity Atlas public 24h board (static, published from the Mac)".
- The site points back to the source repo `sw30labs/singularity-atlas` (code Apache-2.0) for "Run your own copy".
- Pushes go one way. The site has no backend and makes no calls back to the Mac (see ADR 0003).

## Consequences

Positive
- The public surface is limited to what the publisher writes. Private data and code never enter this repo's history.
- The repo can be deleted and rebuilt from the source repo at any time.

Negative
- Changes made directly in this repo can be overwritten or blocked. The publisher (in the source repo, `scripts/publish_public_site.sh`) runs `git reset --hard origin/<branch>` before each publish. It also enforces a **whole-tree file allowlist** and refuses to publish if any other file is present. Only `README.md` is allowlisted outside the generated layout. **This means `docs/adr/*` and `docs/assumptions.md` would fail that check if merged to `main`** (see A-008).
- This repo has no README and no LICENSE (`license: null` via the API). The "Code Apache-2.0" text on the pages refers to the source repo.

## Alternatives considered

None documented. Possible options included serving from the source repo's `docs/` folder. The publisher's backfill mode reads `docs/index.html`, `docs/snapshot.json` and `docs/board.png` from older source commits ("the old docs/ publishing"), which suggests that was the earlier approach. *Inferred.*

## Evidence

- Commit authorship and messages: `git log` (all commits, `1c78bd8`…`08ab792`)
- `board/index.html` footer: "Daily static snapshot; the live dashboard runs locally … does not ship the Innermost Loop corpus or `ref/`."
- `static-shim.js` header: "nothing reaches the machine that built it. Loaded only by the static build (scripts/build_live_site.py)."
- External: `sw30labs/singularity-atlas` `scripts/publish_public_site.sh` @ `70461b5` (header comment, step 4 sync, final allowlist)
