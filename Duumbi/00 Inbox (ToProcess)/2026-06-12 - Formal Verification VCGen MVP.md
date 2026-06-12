---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/research
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M4
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Formal Verification: VCGen MVP

## Context

[[DUUMBI - Phase 15i - Formal Verification]] (archived) already states the thesis: Dijkstra's correctness vision failed to spread because the gap between source code and formal spec is too wide; DUUMBI's JSON-LD graph closes it — the program is already structured, typed, near-SSA, ownership-annotated, with explicit control flow. Verification pipeline collapses from 8 steps to 3: graph → VCGen (direct traversal) → SMT → Z3/CVC5. The schema already carries optional spec fields (`duumbi:verificationStatus`). Status today: Research.

## Goal

`duumbi verify` exists: given a function with pre/postconditions in the graph, it generates verification conditions via weakest-precondition traversal, feeds Z3, and returns **proved** or a **concrete counterexample**. (Phase 15i kill criterion.)

## Subtasks

1. Contract vocabulary: finalize JSON-LD fields for precondition, postcondition, invariant, and effect annotations on Function/Block/Op nodes; extend the core schema + `jsonld-schema` validation.
2. WP rules per Op: implement the Phase 15i rule table (Const, Add/Sub/Mul/Div with overflow/div-by-zero obligations, Compare, Branch, Call with callee contracts, ArrayGet bounds, ReturnOk/ReturnErr) — start with the integer/bool/array subset, defer strings/IO effects.
3. SMT backend: SMT-LIB v2 emission, Z3 integration (process or bindings), counterexample parsing back to graph node ids + concrete values.
4. Branch-only loops: handle DUUMBI's loop-via-branch pattern with user-supplied invariants; document the limitation honestly. The planned structured Loop op ([[2026-06-12 - Op Set Expansion Tiers]]) provides the natural invariant attachment point — coordinate the two designs.
5. Targets: prove 3+ stdlib functions (abs, max, clamp; one array invariant) and one deliberately broken variant returning a counterexample.
6. Surface integration: `verificationStatus` shown in `describe`, TUI query answers ("can this be formally verified?"), and as evidence in intent/Loop artifacts.
7. Write-up: this is the headline differentiator — research note + blog post with the 8-step vs. 3-step pipeline comparison.

## Acceptance criteria

- `duumbi verify <function>` proves the 3 stdlib targets and produces a counterexample for the broken variant.
- Verification results stored as evidence and queryable.
- Unsupported constructs fail with explicit "not yet verifiable because X", never silently pass.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Compositional Verification Proof Boundaries]]
- [[2026-06-12 - Determinism Program for AI Development]]
