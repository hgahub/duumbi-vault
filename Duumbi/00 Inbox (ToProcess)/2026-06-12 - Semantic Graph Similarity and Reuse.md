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

# Semantic Graph Similarity and Reuse

## Context

Exact reuse via semantic hashes exists conceptually (registry + semantic hash reuse is Delivered per [[DUUMBI - Service and Research Direction]]), but **similar** (not identical) graph retrieval is a research hypothesis. The promised behavior: before the AI builds a function, DUUMBI checks whether an equivalent or near-equivalent module already exists and reuses it — turning the registry/repository into compounding leverage and reducing nondeterminism (reused code is deterministic by definition).

## Goal

A benchmarked similarity-retrieval layer: given an intent or a graph fragment, return ranked candidate modules/functions with compatibility assessment, integrated into the intent pipeline as a "reuse-first" step.

## Subtasks

1. Build the module-reuse benchmark named in the research direction: corpus of intents with known-correct reuse targets; compare text search, embeddings, semantic hashes, and graph-feature methods (degree profiles, op-type histograms, graph kernels/matching).
2. Candidate representation: per-function feature vector stored in `duumbi-registry` at publish time (op counts, signature shape, effect summary, contract hashes).
3. Retrieval API: `POST /similarity/query` on duumbi-registry; CLI `duumbi search --semantic`.
4. Compatibility check: signature/type/ownership compatibility + (later) contract implication from [[2026-06-12 - Compositional Verification Proof Boundaries]] — "is the candidate's contract strong enough for this call site?".
5. Intent pipeline integration: reuse-first gate before generation; measure reuse hit rate and token savings on the M1 eval corpus (methodology shared with [[2026-06-12 - Token Economics Benchmark]] — reuse is the single largest token lever, since a hit saves ~100% of that function's generation and repair tokens).
6. Honest metric reporting: precision/recall of each method on the benchmark; pick the simplest method that wins.

## Acceptance criteria

- Benchmark published with method comparison numbers.
- Intent execution demonstrably reuses an existing module instead of regenerating it, with evidence in the run artifact.
- Reuse hit rate tracked as a release metric.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
- [[2026-06-12 - Token Economics Benchmark]]
