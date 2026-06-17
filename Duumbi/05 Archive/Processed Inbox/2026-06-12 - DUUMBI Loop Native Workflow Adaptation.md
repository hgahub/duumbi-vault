---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M3
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# DUUMBI Loop: Native Workflow Adaptation

## Context

`hgahub/duumbi-loop` contains only the plan (`wiki/duumbi-loop-codex-task.md`, ~2,400 lines) — zero code. The plan is excellent on architecture (Axum API, webhook receiver, workers, PostgreSQL, artifact schemas, intake/spec/review/closure state machine, model catalog, RBAC) but is **entirely GitHub/GitLab-centric**: triggers are issue/PR comments, the knowledge graph is built from git repos via tree-sitter, and spec artifacts land as git PRs. DUUMBI itself does not use git for programs — programs are semantic graphs. Loop must be re-scoped so the DUUMBI-native flow is first-class.

## Goal

The intake → spec → review → closure loop runs on DUUMBI intents and graph events with no git dependency; GitHub/GitLab remain optional provider adapters for teams that do use git.

### How Loop maps onto the existing mode model

The three interaction modes (Query / Intent / Agent, delivered) already cover the middle of the cycle; Loop supplies the missing macro-stages around them:

| Loop stage | DUUMBI-native realization | Status today |
|---|---|---|
| Intake | Query-mode investigation captured as an intake artifact (sources, affected graph regions, clarifying questions) | Query exists; capture/artifact missing |
| Spec | `intent create` + `intent review` (+ BDD artifacts per #673) | Exists; hardening in [[2026-06-12 - Intent Create Hardening]] |
| Implement | `intent execute` with dynamic agent teams | Exists |
| Review | **Missing**: independent verification stage comparing the produced graph against spec/BDD/contracts, by a different model/agent than the one that built it | New |
| Closure | **Missing**: knowledge candidates, registry evidence publish, session summary — the learning step | [[2026-06-12 - Active Learning Loop]] |

Today the native cycle ends at execute → archive; review and closure are the two stages Loop must add rather than duplicate what modes already do.

## Subtasks

1. Rewrite the Loop plan's provider layer: introduce `provider-duumbi` as the primary implementation of the `provider-core` trait. Triggers come from DUUMBI surfaces (TUI/Studio/CLI: "request intake on this intent") and session-ledger events, not webhook comments. GitHub (`@duumbi intake` comments) becomes adapter #2, GitLab #3 (de-prioritized).
2. Map the loop objects: git issue → DUUMBI intent record; PR diff → GraphPatch / graph snapshot diff; spec PR → spec artifact attached to the intent (and stored in the evolved `duumbi-registry`); merge commit → approved patch application; closure indexing → new graph snapshot publish.
3. Knowledge base source: for DUUMBI-native projects, skip tree-sitter entirely — the JSON-LD graph is already the index. The Loop plan's tree-sitter pipeline applies only to the git adapters and to [[2026-06-12 - Code Import to Semantic Graph]].
4. Artifact schemas: keep the plan's intake/spec/review/closure artifact contracts (confidence, effort, BDD, evidence, sources) — they port cleanly; align BDD with [[2026-06-12 - Intent at Scale Multi-Module and BDD]].
5. MVP re-scope: P1 = DUUMBI-native intake+spec end-to-end against a local/CLI workspace and the graph-aware `duumbi-registry`, before any GitHub App work. Decide what runs locally (CLI-invoked loop) vs. hosted (cloud Loop service).
6. Reuse decisions from the original plan that stand: Rust workspace monorepo, PostgreSQL metadata, model catalog with daily refresh, idempotency keys, audit events, RBAC roles.
7. Native review stage: independent spec-compliance check (produced graph vs acceptance criteria/BDD/contracts) run by a different model/agent than the implementer, producing a review artifact — the native equivalent of the Loop plan's PR review.
8. Native closure stage: wire [[2026-06-12 - Active Learning Loop]] knowledge-candidate generation + registry evidence publish as the cycle's final step, so every completed loop iteration improves the knowledge base.
9. Update `wiki/duumbi-loop-codex-task.md` (or supersede it with a v2 doc) reflecting this adaptation; scaffold the repo (workspace, CI, README, AGENTS.md).

## Acceptance criteria

- A DUUMBI intent can go through intake → spec → review → closure with artifacts and evidence, with no git repository involved.
- The same loop optionally triggers from a GitHub issue comment via the adapter, proving the provider abstraction.
- Loop writes/reads knowledge through the evolved `duumbi-registry` graph database, not its own parallel graph store.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Session Kernel and Event Ledger]]

## Triage result
- Date: 2026-06-17T17:06:51.903Z
- Classification: execution work
- Routing: Created GitHub issue #738 and routed it to Needs Human Acceptance.
- GitHub artifacts:
  - https://github.com/hgahub/duumbi/issues/738
- Obsidian artifacts:
  - none
- Canonical duplicate:
  - none
- Open questions:
  - See GitHub issue.
- Assumptions:
  - Automated triage refill selected this source as actionable. Rationale: The DUUMBI Loop native adaptation is the next core workflow foundation needed (M3 milestone) to make the full development loop first-class on DUUMBI’s own primitives. No existing eligible Todo issue represents this work, and the inbox note contains a clear scope ready for human acceptance.
- Next stage: Needs Human Acceptance
