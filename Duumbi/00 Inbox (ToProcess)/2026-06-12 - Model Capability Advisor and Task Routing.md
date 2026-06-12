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
related_issues:
  - hgahub/duumbi#675
---

# Model Capability Advisor and Task Routing

## Context

The user's pain: if the models available under their configured providers are likely inadequate for a task (especially intent create), DUUMBI should say so **before** the work fails. The building blocks already exist but are disconnected (verified 2026-06-12):

- The model catalog (`src/agents/model_catalog.rs`, DUUMBI-675) carries `quality`, `speed`, `cost_efficiency`, `reasoning`, `coding`, `lifecycle` per model — but is **documentation-only at runtime**; nothing reads it for decisions.
- Model performance tracking (`src/agents/model_performance.rs`) appends per-call events (outcome incl. ParseFailure/ValidationFailure, latency, cost) and keeps EWMA aggregates per model — but is **passive telemetry**, never consulted.
- Provider selection happens once at init (`src/agents/factory.rs`); the fallback chain only handles transient errors. There is **no per-task routing**: intent clarify, spec generation, mutation, repair, and query all hit the same model.
- Failure records know which provider/model failed at what task type — also unused for selection.

## Goal

Every LLM-calling step declares a capability profile; DUUMBI evaluates the user's configured models against the catalog plus local performance history, warns with a recommendation before work starts, and routes each step to the best adequate configured model.

## Subtasks

1. Task capability profiles as data: intent-clarify (fast, cheap), intent-spec (reasoning + structured output), mutation (coding + tool use), repair (coding), query (cheap, long context), verification-assist. Each declares required vs preferred capabilities.
2. Preflight advisor: at intent create/execute start (and as `duumbi provider doctor`), score each configured model against the step's profile using catalog metadata + local aggregates (e.g. ">40% parse-failure EWMA on intent-spec → risky"). Output: adequate / risky / inadequate with a concrete recommendation ("model X under your providers fits better; or enable provider Y"). Warn non-blocking; block only on hard incompatibility (e.g. tool-use required but unsupported).
3. Per-task routing: extend `ModelSelectionContext` so the factory resolves a model per task profile, not once per session; effort level ([[2026-06-12 - Effort Levels and Cost Control]]) shifts the tier up/down; fallback chain stays for transient errors.
4. Close the loop on passive data: advisor consumes model-performance aggregates and failure records; enforce strategy/failure-pattern deprecation (`agent_knowledge.rs` marks >70%-fail patterns deprecated but selection ignores it today).
5. Catalog as runtime input: the DUUMBI-675 user-approved local catalog becomes the advisor's source; catalog gains task-type evidence fields fed by create/execute evals ([[2026-06-12 - Intent Create Hardening]] subtask 6).
6. Surfaces: TUI warning panel on intent create/execute; CLI `provider doctor` table (per task type × configured model adequacy); advisor verdicts logged to the session for auditability.

## Acceptance criteria

- Configuring a known-weak model and running intent create yields a pre-call warning naming the risk and a recommendation — before tokens are spent.
- With two adequate models configured, spec generation and mutation demonstrably route to different models per their profiles.
- Advisor predictions validated against eval outcomes (a model marked "inadequate" indeed fails the eval; "adequate" passes) on the phase15-e2e corpus.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Intent Create Hardening]]
- [[2026-06-12 - Effort Levels and Cost Control]]
- [[2026-06-12 - Active Learning Loop]]
