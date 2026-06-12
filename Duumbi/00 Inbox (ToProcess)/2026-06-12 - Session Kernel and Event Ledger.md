---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M2
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Session Kernel and Event Ledger

## Context

Today sessions live in `.duumbi/session/current.json` + history archives, scoped to the local TUI/REPL. The product vision: **if the user permits it, a started session can be continued on any surface** (CLI, TUI, Desktop, Cloud, Mobile). The archived roadmap already names the solution: a shared session kernel and an append-only event ledger. This must exist before building Desktop/Cloud/Mobile, otherwise each surface forks its own session model.

## Goal

One session model: an append-only event ledger (turns, queries, mutations, approvals, builds, evidence) that every surface reads/writes through the same kernel API, replayable to reconstruct state, syncable when the user opts in.

## Subtasks

1. Specify the event schema: event types (query, intent, patch proposed/approved/rejected, build, run, telemetry, provider call), envelope (session id, surface, actor, timestamps, hashes), and JSON-LD alignment with the existing graph vocabulary.
2. Session kernel crate in `hgahub/duumbi`: load/append/replay/archive; migrate `SessionManager` and the REPL `/resume` flow onto it without breaking existing archives.
3. Replay semantics: reconstruct conversation + workspace context from the ledger; define what is replayed vs. referenced (large artifacts by hash).
4. Surface adoption: CLI one-shot commands append to the same ledger as the TUI; Studio (WebSocket `/ws/chat`) reads/writes the same session.
5. Sync design (spec only at this stage): opt-in push/pull of ledgers to the evolved `duumbi-registry` graph database; conflict rule (append-only ⇒ merge by event time + causal ids); privacy contract (local-first default, explicit consent to sync).
6. Security: redaction rules for secrets in events; per-session encryption considerations for synced ledgers.

## Acceptance criteria

- TUI, CLI, and Studio share one session: start a query in the TUI, see and continue it in Studio on the same machine.
- Ledger replay reproduces session state byte-for-byte from events.
- Written sync spec approved before any cloud implementation (gates M3/M6 surfaces).

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Determinism Program for AI Development]] (shared evidence ledger)
