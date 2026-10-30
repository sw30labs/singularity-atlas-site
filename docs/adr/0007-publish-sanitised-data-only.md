# 0007. Publish only sanitised data: no corpus, no model names, no raw errors

Status: Accepted (retrospective, inferred 2026-10-25)

## Context

The local state includes private material (Innermost Loop issue text, `ref/`, local LLM details, and raw fetch errors with URLs). It also includes public material (feed headlines, RSS and arXiv excerpts, launch data).

## Decision

The baked public data is reduced on purpose:

- `state.llm` is always `{"available": false, "preferred": null, "active": null}`. `brief.model` is `"heuristic"` or the generic `"local LLM"`, never a model name. All 14 dated snapshots read `"heuristic"`.
- Feed errors are replaced with `"temporarily unavailable"`. 2026-10-09 published raw `HTTPStatusError … Too Many Requests for url …` strings; 2026-10-25 shows the generic text.
- `ingest_running` is `false` and `on_this_date` is `null`.
- `archive-entity/*.json` and `graph/*.json` `loop_editions` carry Innermost Loop edition number, date, title, URL and mention count only. No text.
- Archive search is a redirect to Substack, with no local index (ADR 0003).
- Story `summary` excerpts from RSS and arXiv **are** published.
- The board footer states: "public feeds plus the on-machine brief. It does not ship the Innermost Loop corpus or `ref/`."

## Consequences

Positive
- No private corpus, local paths, hostnames (other than the old localhost footer, see ADR 0004) or model identifiers in current data. This run's scan found no key, `UBS`, `grey_swan` or D.E. Shaw strings.

Negative
- The public brief is labelled heuristic even when the local brief came from an LLM, and the "Daily Loop" may differ from the local one.
- Republishing third-party summaries is a content and licensing choice whose basis isn't recorded (A-011).
- Older dated pages still say "local live is `http://localhost:8055`" (port disclosure only).

## Alternatives considered

Publishing the raw state. That is effectively what the first board pages did with raw error strings (`1c78bd8`).

## Evidence

- `data/state.json` (`llm`, `feeds.*.error`, `last_ingest.errors`, `ingest_running`, `on_this_date`), `data/archive-entity/openai.json`, `data/graph/openai.json`
- `2026-10-09/index.html` vs `2026-10-25/index.html` (feeds list, footer)
- `jq .brief.model 20*/snapshot.json` → 14 × `"heuristic"`
- External: `scripts/build_live_site.py` `sanitize_state()`, and the leak scan and structural scan in `scripts/publish_public_site.sh`
