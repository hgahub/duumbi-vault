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

# Determinism Program for AI Development

## Context

DUUMBI's core promise is making non-deterministic AI-based development deterministic. Today the substrate (compiler, graph, validation) is deterministic but the AI layer is probabilistic, and we have no measurement of how stable intent → graph outcomes are. [[DUUMBI - Service and Research Direction]] lists a replay experiment; issue #684 (Semantic Rewrite Engine as formal graph transformation substrate) is the structural half of the same story: mutations as rule-based graph rewrites rather than freeform LLM output.

## Goal

Determinism becomes a measured, improvable property: identical intent + locked context replays to an identical (or semantically equivalent) graph, and every divergence is explained by the evidence ledger.

## Subtasks

1. Replay harness: lock model, prompt, context pack, graph snapshot, and registry state; run the same intent N times; measure graph-diff stability (exact match, semantic-hash match, behavioral match via tests).
2. Define and publish a determinism metric (e.g. semantic-hash agreement rate across replays) per task class.
3. Constrained mutation: advance #684 — mutations expressed as typed graph-rewrite operations (patch DSL) the LLM selects/parameterizes, instead of emitting raw graph JSON. Smaller output space ⇒ higher determinism, and far fewer output tokens (the same mechanism serves [[2026-06-12 - Token-Efficient Graph Representation for LLM IO]]).
4. Canonicalization: deterministic node-ID assignment, ordering, and formatting so that equivalent graphs serialize identically (prerequisite for diffing and proof caching).
5. Evidence ledger: every AI decision (provider, model, prompt hash, context hash, output hash) recorded append-only — overlaps with [[2026-06-12 - Session Kernel and Event Ledger]].
6. Tie verification in: a replayed graph that passes the same BDD + (later) formal verification is "deterministic enough" even if not byte-identical — define the equivalence ladder explicitly.

## Acceptance criteria

- Determinism dashboard/metric exists and is tracked per release.
- Rewrite-rule-based mutation path demonstrably improves replay stability vs. freeform patches on the same corpus.
- Public write-up: "How DUUMBI makes AI development deterministic" with data.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
- [[2026-06-12 - Formal Verification VCGen MVP]]

## Triage result
- Date: 2026-06-16T05:55:51.079Z
- Classification: execution work
- Routing: Created GitHub issue #720 and routed it to Needs Human Acceptance.
- GitHub artifacts:
  - https://github.com/hgahub/duumbi/issues/720
- Obsidian artifacts:
  - none
- Canonical duplicate:
  - none
- Open questions:
  - See GitHub issue.
- Assumptions:
  - Automated triage refill selected this source as actionable. Rationale: Current Needs Human Acceptance count is 2, below target of 3. No eligible existing Todo issue represents the Determinism Program, a high-priority M1 item with clear scope and strong linkage to existing work (#684, session kernel, rewrite rules). Creating this issue fills the queue with the next actionable strategic work.
- Next stage: Needs Human Acceptance
