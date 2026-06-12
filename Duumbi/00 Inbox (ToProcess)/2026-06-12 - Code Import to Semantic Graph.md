---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/research
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/high
created: 2026-06-12
milestone: M5
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Code Import to Semantic Graph

## Context

[[DUUMBI - Service and Research Direction]] lists code import as a research hypothesis: required if DUUMBI should reuse existing repositories, not only DUUMBI-native modules. Without import, DUUMBI only serves greenfield projects. The Loop plan's tree-sitter indexing pipeline (symbols, imports, call/reference edges, 9 languages) is a useful **shallow** layer, but true import means producing executable DUUMBI Op graphs, which is much harder.

## Goal

A two-tier import story: (Tier A) shallow knowledge import — index existing codebases into queryable knowledge graphs (Loop-style, tree-sitter) so `query` works over legacy code; (Tier B) deep import — translate selected functions into executable DUUMBI Op graphs with verified behavioral equivalence.

## Subtasks

1. Scope decision: which tier serves which product (Tier A → Loop knowledge base + query; Tier B → reuse/migration). Confirm Tier A ships first.
2. Tier A: adopt/build the tree-sitter pipeline from the Loop plan (`codegraph-parser`), emitting knowledge-graph JSON-LD (distinct vocabulary from executable Op graphs); store in the graph-aware `duumbi-registry`.
3. Tier B research spike: pick one source language subset (suggestion: simple Rust or Python functions over ints/strings/arrays) → lift to Op graph via AI translation + test-based equivalence checking (run original vs. DUUMBI build on generated inputs).
4. Equivalence evidence: imported function ships with the test corpus proving behavioral match; mark imported graphs' provenance.
5. Define the boundary honestly in docs: imported knowledge (queryable) vs. imported behavior (executable) — avoid overclaiming.
6. Benchmark: import a real small library and measure how much becomes executable vs. knowledge-only.

## Acceptance criteria

- Tier A: `duumbi knowledge`/`query` answers structural questions over a non-DUUMBI repo.
- Tier B: ≥10 real functions imported to executable graphs with passing equivalence tests.
- Research note with the measured feasibility of deep import.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Semantic Graph Similarity and Reuse]]
- [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]]
