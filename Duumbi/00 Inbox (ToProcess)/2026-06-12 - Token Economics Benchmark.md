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
  - hgahub/duumbi#682
---

# Token Economics Benchmark

## Context

*Proposed addition (Claude, 2026-06-12).* Token usage is cost, latency, and quota (the project already caps Anthropic eval spend). Text-language agentic tools (Claude Code, Cursor, Copilot agents) spend tokens in a characteristic pattern: context discovery dominates input (grep/read cycles, whole-file reads, re-reads after edits — often 50–80% of input tokens), generation output is the highest unit price (output ≈ 5× input on typical pricing), and every repair iteration replays the whole loop. DUUMBI's architecture attacks each of these — and the claim should be **measured, not asserted**. [[DUUMBI - Service and Research Direction]] already names the experiment ("task quality with full context vs. selected context vs. graph summaries"), session LLM usage stats exist (`src/session`), and issue #682 (MCP model usage telemetry analytics) is the natural instrumentation home.

### Where the advantage is expected (per cost center)

| Cost center in text-based tools | DUUMBI mechanism | Expected effect |
|---|---|---|
| Context discovery (file dumps, search cycles) | Graph queries: dependency closure, neighborhood, "where does behavior live" — the graph IS the index | Scoped query answers replace multi-round file reads; expected 5–20× reduction in discovery tokens |
| Generation output (full file/hunk rewrites at ~5× input price) | GraphPatch ops; rewrite-rule selection + parameters (#684) | Small mutation ≈ 10–20% of regenerating the unit; rule emission is tens of tokens |
| Repair iterations (full-context retries after build/test failures) | Pre-build schema/type/ownership validation, node-precise machine-readable errors, deterministic compiler feedback | Fewer retries; retry prompts shrink to error + offending nodes instead of context re-dump |
| Re-derived knowledge across sessions | Session ledger + knowledge graph + query mode | "What did we decide/build" is a query, not re-exploration |
| Reimplementing existing functionality | Registry semantic-hash + similarity reuse-first gate | A reuse hit saves ~100% of that function's generation+repair tokens; hit rate compounds as the registry grows → **declining marginal token cost per task** |

### The honest liability

Raw JSON-LD is 3–10× more verbose than equivalent source code (plus branch-only control flow inflates op counts). "No programming language" is a token **disadvantage** at the representation level unless LLM-facing I/O is engineered so the model never reads or writes raw JSON-LD — that work is [[2026-06-12 - Token-Efficient Graph Representation for LLM IO]]. Without it, novel-generation tasks could be break-even or worse; with it, the architectural levers come on top.

## Goal

A published, reproducible benchmark quantifying tokens-per-task for DUUMBI vs. a text-language agent baseline on the same task corpus, with per-phase attribution — and token-per-task tracked as a standing release metric.

## Subtasks

1. Per-phase token attribution: tag every LLM call with phase (context/discovery, generation, repair, verification, reuse-lookup) in session usage stats and telemetry; align with #682 so the analytics are queryable via MCP too.
2. Benchmark protocol: same corpus (M1 multi-module intents + flagship example), arm A = DUUMBI intent pipeline, arm B = off-the-shelf agent implementing the same spec in Rust; identical model/provider; record input/output/cache tokens, retries, wall-clock, success rate.
3. Lever decomposition — measure each independently: (a) reuse hit vs. fresh generation delta; (b) graph-query context vs. file-read context for identical questions; (c) patch/rewrite-rule output size vs. full-function text; (d) repair iteration count and per-iteration token cost.
4. Run the context experiment from the research direction: full context vs. selected context packs vs. graph summaries — quality-vs-token tradeoff curve.
5. Reuse compounding curve: token cost per task as a function of registry size / reuse hit rate (simulated on the corpus), demonstrating declining marginal cost.
6. Token-per-task as a release metric next to determinism and reuse hit rate; publish "The token economics of graph-native development" (Phase 14 GTM) — including where DUUMBI loses and how the representation work fixes it.

## Acceptance criteria

- Every LLM call in telemetry is phase-attributed; per-task token breakdown reproducible from session data alone.
- Published benchmark with the four-lever decomposition against the text baseline on the shared corpus.
- Token-per-task and reuse hit rate appear in release reporting from v0.5 onward.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Token-Efficient Graph Representation for LLM IO]]
- [[2026-06-12 - Semantic Graph Similarity and Reuse]]
- [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
- [[2026-06-12 - Agent Substrate MCP First-Class]]
