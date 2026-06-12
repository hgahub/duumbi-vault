---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
related_issues:
  - hgahub/duumbi#684
---

# Token-Efficient Graph Representation for LLM I/O

## Context

*Proposed addition (Claude, 2026-06-12).* JSON-LD is the right durable format — but it is the wrong LLM wire format: raw graphs cost 3–10× the tokens of equivalent source code (verbose ids, structural ceremony, branch-only control flow). If LLMs read and write raw JSON-LD, the architectural token advantages (reuse, scoped context, small patches — see [[2026-06-12 - Token Economics Benchmark]]) get eaten by representation overhead, and "no programming language" becomes a cost instead of a feature. The fix is a projection layer: **the LLM never sees or emits raw JSON-LD.** The pieces already half-exist: `describe` produces human pseudo-code, GraphPatch is a structured mutation format, and the semantic rewrite engine direction (#684) shrinks mutation output to rule-id + parameters.

## Goal

Token-minimal LLM I/O over the graph: a canonical, deterministic, compact textual projection for reading, and structured patch/rule emission for writing — measured at token parity or better with an equivalent source-text workflow for novel generation, so the architectural levers come on top rather than paying off representation debt.

## Subtasks

1. Read path: canonical compact projection (describe-grade pseudo-code with stable short node-id anchors) becomes THE format LLMs receive for graph content; deterministic and diff-stable across turns so prompt caching keeps working (cache reads are ~10× cheaper than fresh input — stability is money).
2. Zoom levels: module summary → function signature + contract → block → op detail; context packs select the zoom per node, never the whole graph (integrates the Phase 10 context system and the research direction's graph-summaries experiment).
3. Write path: token-budgeted patch DSL (target: a small mutation ≤ ~10% of the tokens of regenerating the function as text); rewrite-rule selection + parameters as the preferred mutation output, aligning with #684.
4. Id scheme audit: long UUIDs in LLM-facing text burn tokens at every mention — short stable aliases in the projection, mapping layer back to canonical graph ids.
5. Round-trip safety: projection → LLM patch → graph application is semantics-preserving and validated by tests; ambiguity in the projection must fail loudly, never silently misapply.
6. Measure (feeds the benchmark): read+write token cost on the M1 corpus in projection form vs. raw JSON-LD vs. equivalent Rust source; publish the deltas.
7. Orchestrator prompt diet: the mutation system prompt is ~44 KB (~15k tokens) of static text sent on every call (`src/agents/orchestrator.rs`) — restructure into a small core + task-profile-scoped sections (selected by task type/complexity), and measure the per-call saving; cache-stable prefix ordering preserved.

## Acceptance criteria

- No LLM-facing flow (intent, mutation, repair, query answers, MCP tools) serializes raw JSON-LD into prompts.
- Projection + patch output reaches ≤1× the tokens of an equivalent source-text workflow for novel generation on the benchmark corpus.
- Projections are deterministic (byte-stable for unchanged graphs) — verified, since prompt-cache hits depend on it.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Token Economics Benchmark]]
- [[2026-06-12 - Determinism Program for AI Development]]
- [[2026-06-12 - Agent Substrate MCP First-Class]]
