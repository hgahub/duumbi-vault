---
tags:
  - project/duumbi
  - doc/roadmap
  - doc/product-strategy
status: active
created: 2026-05-04
updated: 2026-05-04
related_maps:
  - "[[DUUMBI Roadmap Map]]"
  - "[[DUUMBI Core Concepts Map]]"
  - "[[DUUMBI - PRD]]"
---
# DUUMBI -- Product Roadmap 2026-05

> Current roadmap snapshot as of 2026-05-04. GitHub issues are the source of truth for completion state; this note translates that state into product sequencing.

## 1. Current State

DUUMBI has completed the foundation arc from compiler proof-of-concept through the dynamic agent and MCP platform. Phases 0-12 are treated as delivered product foundation, not future work.

| Area | State | Evidence |
|---|---|---|
| Compiler, CLI, AI mutation, visualization | Delivered | Phases 0-3 complete |
| Modules, intent workflow, Studio, registry/auth | Delivered | Phases 4-8 complete |
| Types, stdlib/build, multi-LLM, showcases | Delivered | Phase 9a and 9A-9C complete |
| Intelligent context, CLI UX, dynamic agents, MCP | Delivered | Phases 10-12 complete |
| Studio Workflow Redesign | Active | Phase 15 is 10/13; #486 closed, #487/#488/#489 open |
| Windows and cross-platform | Planned/partial | Phase 16 is 1/4; #421 closed, #420/#422/#423 open |
| Self-Healing and Telemetry | Planned | No open `phase-13` GitHub issues found during refresh |
| Marketing and Go-to-Market | In progress | Phase 14 still has open content/community/ecosystem issues |

## 2. Immediate Delivery Focus

### Phase 15 -- Studio Workflow Redesign

Goal: prove the Studio and CLI REPL can complete representative intent-driven development tasks with real LLM interaction and inspectable graph/build evidence.

Current GitHub state:
- #486 Calculator E2E: closed on 2026-05-03.
- #487 String Utilities E2E: open.
- #488 Math Library E2E: open.
- #489 E2E protocol documentation: open.

Completion criteria:
- String utilities sample passes in both CLI REPL and Studio.
- Math library sample passes in both CLI REPL and Studio.
- `docs/testing/phase15-walkthrough.md` documents the three sample protocols, timing targets, prerequisites, troubleshooting, and evidence expectations.
- The roadmap hub is updated from partial to done only after #487, #488, and #489 are closed.

## 3. Next Sequenced Work

### 1. Close Phase 15

Finish the remaining kill criterion work before expanding the product surface. This is the shortest path to credible evidence that DUUMBI Studio is not just a visualization shell but a working intent-driven development interface.

### 2. Complete Phase 16 -- Windows and Cross-Platform Support

Phase 16 should follow Phase 15 because it is small, concrete, and removes a visible platform limitation.

Open work:
- #420 add `windows-latest` to the GitHub Actions CI matrix.
- #422 verify Windows terminal color behavior and fallback.
- #423 document Windows requirements in README.

Already completed:
- #421 path separator audit for module names.

Kill criterion: `cargo test --all` passes on `windows-latest` CI and Windows install requirements are documented.

### 3. Plan and Start Phase 13 -- Self-Healing and Telemetry

Phase 13 should not start as vague "autonomous improvement." It needs issue-backed scope first.

Minimum issue breakdown:
- Runtime traceId/nodeId mapping and persisted telemetry events.
- Error classification that can identify repairable graph failures.
- Repair-agent prompt/input contract.
- Patch generation, validation, and test rerun loop.
- Human approval gate before applying repair patches.
- Evidence protocol proving runtime error -> nodeId -> valid patch -> tests green.

Kill criterion: a controlled runtime failure maps to the graph node that caused it, produces a valid repair patch, reruns validation/tests, and reaches a green state with an auditable trail.

### 4. Continue Phase 14 -- Marketing and Go-to-Market

Phase 14 can continue in parallel, but launch claims should follow product evidence. The most credible story after Phase 15 is:
- semantic graph compiler foundation is real.
- intent-driven graph mutation works.
- Studio can execute representative tasks.
- dynamic agents and MCP are already delivered.

Do not over-market self-healing, remote sync, or enterprise control-plane capabilities before Phase 13 and the session/control-plane architecture are specified.

### 5. Multi-Surface and Session Roadmap

After Phase 15/16 and Phase 13 planning, define the multi-surface architecture as product infrastructure:
- shared session kernel.
- append-only event ledger.
- runner abstraction.
- shared UI/API protocol.
- Studio web/desktop path.
- thin Slack/Discord bridge.
- enterprise control-plane/execution-plane split.

This should be treated as an architecture milestone before adding broad cloud sync or chat-command surfaces.

## 4. Product Sequencing

| Order | Work | Why Now |
|---|---|---|
| 1 | Finish Phase 15 E2E samples and docs | Proves Studio + CLI REPL workflow with real tasks |
| 2 | Finish Phase 16 Windows CI/docs | Small scope, removes platform credibility gap |
| 3 | Specify Phase 13 issues | Converts self-healing from vision to executable work |
| 4 | Continue Phase 14 GTM | Market evidence-backed capabilities |
| 5 | Specify session kernel and event ledger | Required before multi-device sync and desktop/chat surfaces |
| 6 | Build Studio web/desktop path | Reuse one frontend instead of splitting product surfaces |
| 7 | Add Slack/Discord operational bridges | Useful after session/approval model is stable |
| 8 | Add enterprise control plane | Requires policy, audit, runners, and sync model first |

## 5. Risks and Constraints

- **Surface fragmentation:** CLI, Studio, desktop, and chat must share a session/domain protocol or they will become separate products.
- **Premature cloud sync:** syncing whole workspaces or prompts by default creates privacy and conflict risks.
- **Weak Phase 13 scope:** self-healing needs traceability, repair contracts, validation, and human approval; otherwise it becomes an unauditable agent loop.
- **Windows credibility:** README still says Windows is not supported until Phase 16 completes.
- **Marketing drift:** public claims should track evidence from issues and test protocols, not long-term architecture intent.

## Related Documents

- [[DUUMBI - PRD]] -- current product strategy and architecture
- [[DUUMBI Roadmap Map]] -- roadmap hub
- [[DUUMBI Core Concepts Map]] -- conceptual overview
- [[DUUMBI - Phase 15 - Studio Workflow Redesign]] -- active Studio milestone
- [[DUUMBI - Phase 16 - Windows & Cross-Platform Support]] -- cross-platform milestone
- [[DUUMBI - Post-MVP Roadmap]] -- older business roadmap, now historical/supporting context
