---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
related_issues:
  - hgahub/duumbi#689
  - hgahub/duumbi#673
---

# Intent at Scale: Multi-Module and BDD

## Context

All passing intent-execute evals today are single tiny functions. Issue #689 asks for multi-function / multi-module intent-execute eval at scale; issue #673 asks for BDD specification artifacts in intent creation and execution. The "AI builds real software on the graph substrate" claim is unproven beyond toys — this is the main credibility gap after the first release.

## Goal

An intent like "build a small HTTP service with persistent storage" decomposes into multiple functions across multiple modules, executes with verification, and the evidence (BDD scenarios, eval pass rates per provider) is published.

## Subtasks

1. Define the scaled eval corpus: 10–20 intents spanning 2+ functions and 2+ modules each (reuse flagship example domains: HTTP, SQLite, JSON, file I/O).
2. Intent decomposition: planner step that splits a large intent into a DAG of function-level intents with declared inter-module dependencies.
3. BDD artifacts (#673): intent creation emits Gherkin scenarios; intent execution validates against them; store as evidence next to the intent record. Aligns with the Loop plan's spec artifact format (`bdd.feature`).
4. Eval harness extension (`phase15-e2e` style): per-provider pass rates, token cost, retry counts; respect the Anthropic budget cap (use MiniMax tier for iteration). Phase-attributed token recording per [[2026-06-12 - Token Economics Benchmark]] so this corpus doubles as the token benchmark substrate.
5. Failure taxonomy: classify failure modes (wrong decomposition, type errors, ownership violations, hallucinated ops) to feed the determinism program.
6. Publish results as a benchmark page/blog post (Phase 14 GTM material).

## Acceptance criteria

- ≥70% pass rate on the multi-module corpus with the primary provider, fully automated.
- Every executed intent carries BDD scenarios + verification evidence.
- Results reproducible from a single eval command.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Determinism Program for AI Development]]
- [[2026-06-12 - Flagship Examples and Showcase Programs]]

## Partial completion note
- Date: 2026-06-14
- BDD artifacts subtask (#673) was delivered by PR #703: https://github.com/hgahub/duumbi/pull/703
- Merge commit: `0855a44b0f20ce1c8603891ec57d9eb075c0f956`
- Remaining scope: the broader intent-at-scale / multi-module eval work remains open through issue #689 and this note should stay in ToProcess until that work is triaged or completed.

