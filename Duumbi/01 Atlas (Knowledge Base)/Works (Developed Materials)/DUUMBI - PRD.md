---
tags:
  - project/duumbi
  - doc/vision
  - doc/product-strategy
status: active
created: 2026-02-15
updated: 2026-05-04
related_maps:
  - "[[DUUMBI Core Concepts Map]]"
  - "[[DUUMBI Roadmap Map]]"
  - "[[DUUMBI - Product Roadmap 2026-05]]"
  - "[[DUUMBI - MVP Specification]]"
  - "[[DUUMBI - Tools and Components]]"
  - "[[DUUMBI - Architecture Diagram]]"
  - "[[DUUMBI - Glossary]]"
---
# DUUMBI -- Product Requirements and Architecture Strategy

> [!important] This document is the current product strategy and long-term PRD for DUUMBI as of 2026-05-04. For milestone execution status, see [[DUUMBI - Product Roadmap 2026-05]] and [[DUUMBI Roadmap Map]]. For terminology, see [[DUUMBI - Glossary]].

## 1. Executive Summary

DUUMBI is an AI-first semantic graph compiler and development platform. Its core thesis remains unchanged: software should be represented as a typed semantic graph in JSON-LD, not as text files in human programming languages. AI agents generate structured graph mutations, the system validates every mutation mechanically, and native binaries are produced only after the graph reaches the [[Semantic Fixed Point]].

The product has moved beyond the early MVP framing. Phases 0-12 are delivered: the compiler pipeline, usable CLI, AI mutation, web visualization, module system, intent-driven workflow, Studio, registry/auth, type-system work, multi-provider LLM support, intelligent context, CLI UX, dynamic agents, and MCP platform are now foundation capabilities. The active product problem is no longer "prove the compiler can work"; it is "turn the delivered foundation into a coherent, multi-surface development system with reliable session continuity."

The strategic direction is to build one product with several surfaces, not five separate products:
- CLI remains the deterministic authority surface and automation contract.
- TUI/interactive CLI remains the fastest keyboard-first local workflow.
- DUUMBI Studio becomes the shared graphical frontend for web and later desktop.
- Slack/Discord integrations are operational bridges for status, assignment, and approval, not primary editing environments.
- A future registry/control plane coordinates identity, policy, session sync, audit, model routing, and remote execution.

## 2. Product Thesis

Traditional AI coding tools operate over text. That creates avoidable failure modes: syntax hallucinations, partial-file context, brittle patching, and weak traceability from user intent to executable behavior. DUUMBI changes the primary artifact from source text to a semantic instruction graph.

The product promise is not that AI becomes infallible. The promise is narrower and stronger: AI output is forced through explicit schemas, graph invariants, type checks, validation, tests, and human approval points before it becomes executable software.

**Core properties:**
- **Representation:** program logic is stored as typed JSON-LD graph data.
- **Compilation:** validated graph nodes lower to Cranelift IR and native binaries.
- **Human role:** developers define intent, inspect plans/diffs, approve mutations, and validate outcomes.
- **AI role:** agents propose and mutate graph structure inside a validation loop.
- **System role:** DUUMBI enforces the graph contract, compilation pipeline, diagnostics, snapshots, history, and policy boundaries.

## 3. Current Product State

### Delivered Foundation

Phases 0-12 are complete in the roadmap and GitHub issue history:
- **Phases 0-3:** JSON-LD to Cranelift proof, CLI, AI mutation, and web graph visualization.
- **Phases 4-8:** interactive CLI/module system, intent-driven development, Studio, registry/distribution, and registry auth/user management.
- **Phases 9a-9C:** type-system completion, stdlib/instruction expansion, multi-LLM providers, benchmark/showcase work.
- **Phases 10-12:** intelligent context/knowledge graph, CLI UX/developer experience, dynamic agent system, and MCP.

### Active Work

Phase 15, Studio Workflow Redesign, is the active delivery focus. GitHub state as of 2026-05-04:
- #486 Calculator E2E is closed, completed on 2026-05-03.
- #487 String Utilities E2E remains open.
- #488 Math Library E2E remains open.
- #489 Phase 15 E2E protocol documentation remains open.

Phase 16 is planned and partially started:
- #421 path separator audit is closed.
- #420 Windows CI matrix, #422 Windows terminal color test, and #423 Windows README requirements remain open.

No open `phase-13` GitHub issues were found during the 2026-05-04 roadmap refresh. Phase 13 remains a planned product milestone, not an active issue-backed execution track.

## 4. User Surfaces

### CLI: Authority Surface

The CLI is the stable contract for automation, CI, scripting, and reproducible local development. It should keep strict stdout/stderr discipline, stable exit codes, machine-readable JSON/JSONL diagnostics, no-color/non-interactive modes, and explicit artifact paths. All higher-level surfaces should reuse the same domain APIs rather than reimplement compiler behavior.

### Interactive CLI and TUI

The interactive CLI/TUI should be the fastest local developer workflow for session navigation, graph inspection, command palette actions, diagnostics, patch preview, build/run output, and resumable intent work. It should sit on the same session and domain APIs as CLI and Studio.

### DUUMBI Studio

Studio is the shared graphical product surface. The current direction is a focused workflow around Intents, Graph, and Build panels, context-aware chat, graph refresh after mutation, provider/template management, and end-to-end evidence for realistic tasks. Longer term, Studio should become the shared frontend that can run in web and desktop contexts.

### Desktop

Desktop should not become a separate native-widget product. The preferred path is to package the shared Studio frontend with a Rust-backed local runner, most likely through a lightweight desktop shell such as Tauri once the core workflow is stable. Desktop should be macOS/Linux first until Windows support is officially validated.

### Slack and Discord

Chat integrations should stay thin and operational. The correct scope is assignment, status, notifications, approvals, short diff summaries, run triggers, and deep links into Studio or CLI instructions. They should not attempt graph editing, complex conflict resolution, or long configuration workflows.

## 5. Target Architecture

The product should be organized around shared kernels and contracts rather than UI-specific behavior.

### Core Compiler and Graph Kernel

The existing parser, graph, validator, compiler, patch, snapshot, config, and agent modules remain the product foundation. This layer owns semantic correctness and compilation.

### Session Kernel

DUUMBI needs a first-class session model that is shared by CLI, interactive CLI/TUI, Studio, future desktop, and future chat bridges. The session kernel should define:
- `session_id`, `workspace_id`, and `device_id`.
- append-only event journal entries for intent, patch, validation, build, run, diagnostic, approval, and artifact events.
- checkpoints for fast replay.
- explicit branch/merge semantics for conflicting work.
- resumability across local surfaces first, and policy-controlled remote sync later.

The natural sync unit is not a whole workspace copy. It is the append-only ledger of graph patches, checkpoints, diagnostics, build/run state, and artifact references.

### Runner Abstraction

Local, CI, and future remote execution should share a runner abstraction. CLI can run locally, CI can run non-interactively, and the future control plane can dispatch isolated runner jobs. This avoids coupling Studio or chat integrations directly to compiler process details.

### UI/API Protocol

CLI, TUI, Studio, desktop, and chat bridges should speak one domain protocol for sessions, graph views, intents, mutations, diagnostics, build/run state, and approvals. UI-specific presentation belongs in clients; product behavior belongs in shared services.

### Control Plane and Execution Plane

Enterprise/cloud DUUMBI should separate the control plane from the execution plane:
- **Control plane:** auth, tenant policy, session index, audit trail, registry metadata, model routing, telemetry aggregation, and user/org settings.
- **Execution plane:** isolated runner jobs for validation, build, test, run, repair, and artifact generation.

For an Azure-oriented enterprise path, a reasonable architecture is Container Apps for services, Container Apps Jobs for finite runner workloads, PostgreSQL for session/policy metadata, Blob Storage for artifacts/snapshots/logs, Redis for ephemeral locks/cache/queues, managed identity, Key Vault, private networking, and explicit egress controls.

## 6. Data, Sync, and Policy

Session sync must be policy-driven from the start. A useful policy matrix should distinguish:
- `session_metadata`
- `graph_patches`
- `snapshots`
- `artifacts`
- `telemetry`
- `prompts`
- `attachments`
- `notifications`

Default posture should be local-first and conservative. Secrets and API keys must never sync as raw values; only references should move across devices or services. Prompts and telemetry are sensitive and should require explicit user or tenant policy decisions before remote sync.

Conflict handling should prefer explicit branches and merges over last-write-wins. CRDT-style automatic merging is not the right first default for a semantic compiler where graph validity and human approval matter more than silent convergence.

## 7. Model and Agent Strategy

DUUMBI should not start by building a proprietary "smart model router." The first router should be deterministic and auditable:
- tenant-approved provider/model allowlists.
- task-category defaults.
- explicit cost ceilings.
- latency, success-rate, rollback-rate, and validation-failure telemetry.
- full audit logs for routing decisions.

The dynamic agent system and MCP work delivered in Phase 12 are the foundation for multi-agent execution. The next step is to harden reliability, evidence, and handoff behavior through Phase 15 and then use Phase 13 to close the loop from runtime telemetry to repair suggestions.

## 8. Delivered, Active, Planned

### Delivered

- Semantic graph representation and Cranelift compilation pipeline.
- CLI commands for init/build/run/check/describe and AI mutation workflows.
- Snapshot/history and undo foundations.
- Web visualization and Studio foundation.
- Module system, registry/distribution, registry auth.
- Intent pipeline, dynamic agents, MCP server/client work.
- Multi-provider LLM support and developer UX improvements.

### Active

- Phase 15 Studio Workflow Redesign.
- Remaining Phase 15 kill criterion tasks: #487, #488, #489.
- Phase 14 go-to-market/content/community/ecosystem work in parallel.

### Planned

- Phase 16 Windows and cross-platform support completion.
- Phase 13 self-healing and telemetry, including runtime error to nodeId mapping and repair loop validation.
- Shared session kernel and append-only event ledger hardening.
- Studio-as-shared-frontend path for web/desktop.
- Thin Slack/Discord operational bridges.
- Enterprise control plane with policy-based sync, audit, and isolated remote runners.

## 9. Success Criteria

Near-term product success should be measured by evidence, not feature count:
- Phase 15 sample tasks complete in both CLI REPL and Studio with real LLM interaction.
- Studio users can create, execute, inspect, build, and run intent-driven graph changes without falling back to raw JSON-LD.
- CLI remains fully scriptable and deterministic.
- Windows support is proven by CI and documented requirements.
- Phase 13 has a concrete issue-backed plan before implementation starts.
- Session continuity is specified before adding broad cloud sync or chat surfaces.

## Related Documents

- [[DUUMBI Core Concepts Map]] -- conceptual overview
- [[DUUMBI Roadmap Map]] -- roadmap hub
- [[DUUMBI - Product Roadmap 2026-05]] -- current roadmap
- [[DUUMBI - MVP Specification]] -- original MVP specification
- [[DUUMBI - Tools and Components]] -- technical stack details
- [[DUUMBI - Architecture Diagram]] -- visual component overview
- [[DUUMBI - Glossary]] -- canonical term definitions
