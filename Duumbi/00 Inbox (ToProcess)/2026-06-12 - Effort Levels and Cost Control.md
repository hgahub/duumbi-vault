---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Effort Levels and Cost Control

## Context

Today the user has no effort/cost lever (verified 2026-06-12): complexity is auto-derived from a crude proxy (test-case count → Simple/Moderate/Complex in `src/agents/analyzer.rs`), the agent team comes from a fixed 9-row lookup table, and `AgentPolicy` has token budgets (`budget_per_intent`, `budget_per_session`, alert threshold) but no user-facing control surface. Cost per call is already recorded (`cost_usd` in `ModelCallEvent`) yet never previewed or summarized for the user. Requested capability: specify an effort level for work, directly influencing cost.

## Goal

A user-selectable effort level (low / medium / high / max) at session, intent, and command level that maps to model tier, retry counts, context budget, team size, and verification depth — with a cost estimate before execution and a cost report after.

## Subtasks

1. Effort plumbing: `--effort` flag on CLI commands (`intent create/execute`, `add`), `/effort` in the TUI, optional `effort:` field in the intent spec YAML, session default in config; precedence: command > intent > session.
2. Mapping table (data, not code): effort → model tier (via [[2026-06-12 - Model Capability Advisor and Task Routing]]), mutation/repair retry counts, context token budget, agent team modifier (e.g. high adds Reviewer even on Moderate tasks), verification depth (tests only → +property tests → +verify when available), clarification pass on/off.
3. User complexity override: allow overriding the derived Simple/Moderate/Complex profile — the test-case-count proxy is often wrong; effort and complexity are distinct inputs to team assembly.
4. Cost preview: before execute, estimate a cost range from catalog pricing × historical per-task token averages (model-performance EWMA) — "estimated $0.12–0.35 at effort=medium; proceed?"; respect the configured alert threshold.
5. Hard budget enforcement: `budget_per_intent` becomes an enforced stop with graceful abort (state saved, resumable), not just an alert; TUI shows budget consumption live during execute.
6. Cost reporting: per-intent cost in `intent status/review` output and archived ExecutionMeta; per-session rollup in session stats; both feed the [[2026-06-12 - Token Economics Benchmark]] release metrics.

## Acceptance criteria

- The same intent run at `--effort low` vs `--effort high` shows measurably different cost, retries, and verification depth, both visible in the report.
- Cost preview is within ±50% of actual on the eval corpus.
- Hitting the intent budget mid-execute stops cleanly and the intent is resumable.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Model Capability Advisor and Task Routing]]
- [[2026-06-12 - Token Economics Benchmark]]
