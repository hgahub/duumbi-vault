---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M0
source: "[[DUUMBI Future Development Roadmap Map]]"
related_issues:
  - hgahub/duumbi#688
  - hgahub/duumbi#382
---

# Flagship Examples and Showcase Programs

## Context

`examples/` in the main repo is empty; showcases are toy-scale (calculator, fibonacci, min_of_two). Issue #688 asks for a flagship reference program (HTTP + SQLite + JSON), and #382 covers Tier 1 stdlib ecosystem E2E smoke tests. The Op surface now covers strings, arrays, structs, ownership, Result/Option, JSON, TCP, HTTP, SQLite, file I/O, and a static server — enough for a real program.

## Goal

`examples/` contains 3–5 real programs, each buildable offline from the released binary, proving the language is more than single tiny functions.

## Subtasks

1. Flagship: small HTTP service backed by SQLite returning JSON (uses stdlib-http/db/json/server) — the #688 program.
2. CLI utility example: file processing with string ops + Result error handling.
3. Network example: TCP echo or HTTP client aggregation.
4. Each example: JSON-LD graph + `describe` output + expected run transcript + one-paragraph README, structured for copy-paste from docs.
5. CI job that builds and runs every example on every PR (examples never rot).
6. Wire #382 ecosystem E2E smoke tests so registry-distributed Tier 1 modules are exercised by at least one example (clean-workspace install evidence).
7. Surface the examples on duumbi.dev showcases section and in docs quickstart follow-ups.

## Acceptance criteria

- `duumbi run` succeeds for every example on a clean machine with the released binary.
- The flagship HTTP + SQLite + JSON example is referenced from the main repo README and in-repo examples guide.
- CI fails if any example breaks.

## Stage 12 Disposition

- 2026-06-15: Subtask 1 / GitHub issue #688 is complete. Implementation PR hgahub/duumbi#715 was merged at `64649fa9e3a3e14e021e2261b00eea872ecee448`, adding `examples/flagship-http-sqlite-json/`, root README/docs links, and focused CI coverage for the loopback HTTP + SQLite + JSON path.
- The source GitHub issue was closed after Stage 12 closure evidence.
- This note remains in `00 Inbox (ToProcess)` because the broader roadmap item is not fully complete: additional showcase examples, duumbi.dev/public-docs surfacing, and every-example CI coverage remain future work.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Release v0.4.0-preview TUI-first]]
