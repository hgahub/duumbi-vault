---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Intent Create Hardening

## Context

User experience: intent create often fails and is slow, and not all models can do it. The code confirms why — create is the least-defended step in the pipeline (verified 2026-06-12):

- **Single LLM call, no retry** (`src/intent/create.rs`) — while execute has a 5-retry escalating ladder, create gets one shot.
- **Free-text JSON output, no schema enforcement** — the model must emit valid JSON inside plain text (`call_plain_completion`); weak/mid models fail exactly here. Parse failure falls back to only **3 hardcoded benchmark specs** (calculator, string-utils, math-library) — everything else just fails.
- **Zero workspace context** — the Phase 10 context pipeline (classify → traverse → collect → budget → few-shot) is used for mutations but NOT for create; the spec is generated blind to existing modules, past intents, or learned examples.
- **No streaming, optional clarification adds a second round-trip** — perceived slowness with no progress feedback.

## Goal

Intent create succeeds reliably on mid-tier models, recovers from malformed output instead of failing, sees the workspace it is planning for, and streams visible progress.

## Subtasks

1. Schema-constrained spec output: use the tool-use / structured-output path for the spec JSON where the provider supports it (capability-gated via [[2026-06-12 - Model Capability Advisor and Task Routing]]); free-text JSON remains only as fallback for providers without it.
2. Retry-with-feedback ladder for create: ≥2 retries where parse/validation errors are returned to the model field-by-field (mirror the execute ladder), instead of one-shot fail.
3. Context enrichment for create: workspace module summary, active intents (duplicate/conflict detection), and top-N similar archived intents from the learning store as few-shot templates — the same machinery mutations already use.
4. Generalize the fallback: replace the 3 hardcoded benchmarks with retrieval of similar archived/published intent specs as templates (local learning store now, registry similarity later).
5. Streaming + phase progress: stream spec generation; show clarify/spec phases in TUI; route the clarification pass to a cheap fast model (advisor decides) or skip it when the description is information-rich.
6. Create-specific eval in `phase15-e2e`: per provider/model success rate for intent create itself (today evals cover the whole flow); results feed the advisor's adequacy data and the model catalog evidence.
7. Track create latency (p50/p95) and success rate in session stats as standing metrics.

## Acceptance criteria

- Create success rate on the eval corpus measurably improves on mid-tier models (baseline measured first; target ≥80% where the same model previously failed).
- A malformed spec response is repaired automatically at least once before user-visible failure, with the error shown.
- Created specs reference existing workspace modules correctly (no blind duplicates).
- User sees streamed progress; clarify+create p95 latency is measured and reported.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Model Capability Advisor and Task Routing]]
- [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
- [[2026-06-12 - Token-Efficient Graph Representation for LLM IO]]
