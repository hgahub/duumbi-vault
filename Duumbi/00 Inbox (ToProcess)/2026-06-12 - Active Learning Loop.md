---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M2
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Active Learning Loop

## Context

Audit finding (2026-06-12): DUUMBI **records** a lot but **adapts** little. What works today: success/failure JSONL auto-appended after every task (workspace + user-local `~/.duumbi/learning/`), few-shot injection of scored past successes, recent failures shown as "avoid" patterns in mutation context. What is passive or missing: session stats are display-only; model-performance aggregates are never consulted; strategy/failure-pattern deprecation (>70% fail rate) exists but selection ignores it; DecisionRecord/PatternRecord are manual-only; query answers can't be saved as knowledge (MODE-011 open); telemetry crash/trace artifacts are inspection-only (Phase 13 loop not closed); and the registry carries **zero** quality/evidence metadata — one user's failures and successes never benefit another.

## Goal

Recorded evidence changes future behavior — locally (routing, prompts, strategy selection, repair) and community-wide (registry evidence metadata) — and "did the system get better?" becomes a measured metric. This is also the closure stage of the native development loop ([[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]]).

## Subtasks

1. Local adaptation (can start in M1): model-performance aggregates feed the advisor/routing ([[2026-06-12 - Model Capability Advisor and Task Routing]]); deprecated strategies/failure-patterns are actually excluded in assembler/orchestrator selection; a periodic aggregation job clusters raw failure records into named failure patterns automatically (today taxonomy is ad-hoc).
2. Auto knowledge capture as closure: a completed intent generates approval-gated DecisionRecord/PatternRecord candidates (what was decided, what pattern emerged, what failed and why) into the knowledge store; implement MODE-011 so query answers can be saved as knowledge nodes; knowledge nodes already flow into query context — closing the loop.
3. Telemetry → repair loop (Phase 13 completion): `telemetry inspect` output feeds an auto-proposed repair intent (crash → mapped graph context → draft intent awaiting approval), instead of stopping at inspection.
4. Community learning via registry: published module versions carry evidence metadata — eval results, verification status, provider/model compatibility ("intent-executed successfully with X") — extending [[2026-06-12 - Registry Graph Database Evolution]]; opt-in anonymized aggregate stats (which model families succeed at which task types) published alongside the model catalog so every user's advisor benefits; explicit trust/provenance model for imported knowledge.
5. Learning effectiveness metric: trend of retry counts, failure rates, and tokens-per-task over workspace lifetime — published in the [[2026-06-12 - Token Economics Benchmark]] reporting so "the system learns" is a claim with data.

## Acceptance criteria

- A failure pattern observed N times measurably changes behavior (warning, prompt content, or routing) with no manual configuration.
- Completing an intent produces knowledge candidates for approval; approved ones appear in subsequent query/mutation context.
- A registry module page shows evidence metadata; the advisor uses community model-compatibility data when local history is empty.
- Learning-effectiveness trends are part of release reporting.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Model Capability Advisor and Task Routing]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
