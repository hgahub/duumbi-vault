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
source: "Manual TUI UX review, 2026-06-12"
parent: "[[2026-06-12 - TUI as Primary Surface Polish]]"
---

# TUI Progress and Session Evidence UX

## Context

A live MiniMax Query-mode request succeeded in the TUI. The UI showed a spinner and "Reviewer agent (minimax/MiniMax-M2.7) is answering", then rendered the answer with sources, confidence, model, and elapsed time. This is useful, but long-running work still lacks stage-level progress, cost/request counters, retry visibility, and a clear sense of where the process is in the workflow.

The archived session file stored the query summary, but usage stats remained zero and `/resume` displayed a filename-like archive id (`20260612 175411 653 1`) instead of a readable local timestamp and summary.

## Goal

The TUI should make provider-backed work observable while it runs and auditable after it finishes. Users should be able to tell what is happening, how long it has been running, whether it is waiting on a provider, how many attempts/calls have happened, and what can be resumed later.

## Observed Evidence

- Query prompt: `what modules exist?`.
- Provider: MiniMax through workspace config.
- Running state: generic spinner plus `Reviewer agent ... is answering`.
- Completion: answer rendered with `Sources: 10 | Confidence: Medium | Model: minimax/MiniMax-M2.7` and `completed in 6.07s`.
- Session archive: one query turn persisted, but usage stats showed `llm_calls: 0`, token counts `0`, successes `0`, and empty provider breakdown.
- `/resume` displayed `[1] 20260612 175411 653 1 (1 turn(s))` rather than a readable date/time and short summary.

## Subtasks

1. Add a progress model for Query, Agent, and Intent operations: preparing context, provider call, streaming/receiving, parsing, validating, writing, building, running.
2. Show elapsed time during work, not only after completion.
3. Show request/attempt/retry count and provider/model in the running state.
4. Record successful Query-mode provider calls in session usage stats and provider breakdown.
5. Improve `/resume` listing with local date/time, mode/task type, first prompt, short summary, provider/model, and success/failure status.
6. Preserve command errors and provider failures as resumable session events.

## Acceptance Criteria

- A long-running provider call gives the user current phase, elapsed time, provider/model, and attempt count.
- `/resume` is understandable without decoding archive filenames.
- Session usage stats reflect successful and failed provider-backed calls.
- Query, Agent, and Intent flows share consistent progress language.

## Links

- [[2026-06-12 - TUI as Primary Surface Polish]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
- [[2026-06-12 - Effort Levels and Cost Control]]
- [[2026-06-12 - Token Economics Benchmark]]
