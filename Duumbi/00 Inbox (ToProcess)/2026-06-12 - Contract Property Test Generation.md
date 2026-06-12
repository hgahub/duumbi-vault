---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Contract-Based Property Test Generation

## Context

*Proposed addition (Claude, 2026-06-12).* Full SMT verification lands in M4, but the contract vocabulary (pre/postconditions on graph nodes) can pay off a milestone earlier: because the graph is already typed and ownership-annotated, input generators can be derived mechanically and contracts checked by property-based testing **now**. This is the cheap rung on the assurance ladder (types → property tests → SMT proof), produces determinism evidence for M1, and forces the contract format to exist before VCGen consumes it — de-risking M4.

## Goal

Functions with contracts get auto-generated property tests: DUUMBI derives input generators from parameter types, runs N randomized cases against the postconditions, shrinks failures to minimal counterexamples, and records results as evidence.

## Subtasks

1. Contract fields: share one JSON-LD contract vocabulary with [[2026-06-12 - Formal Verification VCGen MVP]] (precondition, postcondition, invariant) — define once, consume twice.
2. Type-driven generators: i64/f64/bool ranges, strings, arrays, structs, Option/Result — respecting preconditions as generation filters.
3. Property runner: execute the compiled function (native build) against generated inputs; check postconditions; shrink failing inputs to minimal counterexamples.
4. Evidence records: pass/fail counts, seeds (reproducible), counterexamples stored as evidence linked to the function node; surfaced in `describe`, query answers, and intent/Loop artifacts.
5. Annotate stdlib Tier 1 functions with contracts and wire the property tests into CI.
6. `duumbi check --properties` (or similar) CLI entry; same results feed the [[2026-06-12 - Determinism Program for AI Development]] equivalence ladder (replayed graphs that pass identical property suites are behaviorally equivalent).

## Acceptance criteria

- All stdlib Tier 1 exported functions carry contracts with auto-generated property tests running in CI.
- A seeded failing case reproduces deterministically and reports a minimal counterexample with node ids.
- The same contract annotations are later consumed unchanged by the VCGen MVP.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
- [[2026-06-12 - Determinism Program for AI Development]]
