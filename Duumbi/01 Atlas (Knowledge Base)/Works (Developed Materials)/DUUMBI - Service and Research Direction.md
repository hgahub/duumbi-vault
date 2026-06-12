---
tags:
  - project/duumbi
  - doc/product-strategy
  - doc/research-direction
status: active
created: 2026-05-08
updated: 2026-05-23
related_maps:
  - "[[DUUMBI Core Concepts Map]]"
  - "[[DUUMBI Technical Architecture Map]]"
  - "[[DUUMBI Agentic Development Map]]"
---

# DUUMBI - Service and Research Direction

## Summary

- Fact: This is an internal decision memo, not public marketing copy.
- Interpretation: DUUMBI should be framed as an intent-to-validated-software system, not only as a compiler.
- Interpretation: The strongest current framing is: DUUMBI is a semantic execution substrate for coding agents where semantic graphs make AI-generated behavior inspectable, reusable, testable, and eventually formally verifiable.
- Fact: The first local Phase 13 runtime failure feedback foundation now exists in source: opt-in traced builds, local trace/crash artifacts, graph function/block back-mapping, telemetry inspection, repair crash context, and repair validation evidence contracts.
- Interpretation: The credible self-healing story remains local developer/test runtime failure feedback first: traced runs produce crash evidence, graph back-mapping, repair-ready context, validation, and a human-reviewable patch proposal.
- Assumption: Production customer crash ingestion, DUUMBI account-based telemetry, automatic repair delivery, and silent application updates are later product tracks, not the first Phase 13 slice.
- Assumption: "Current" means the vault, public docs, and source repo state reviewed on 2026-05-08, with runtime failure feedback updated from #580/#604 evidence on 2026-05-23.
- Assumption: When local archive prose and source repo behavior disagree, source repo behavior and GitHub execution state should be treated as the stronger evidence.

## Claim Labels

- Fact: Directly supported by local source files, active vault notes, archived execution docs, public docs, or primary external sources.
- Interpretation: A reasoned conclusion drawn from multiple facts.
- Assumption: A planning default that should be revisited if stronger evidence appears.
- Research hypothesis: A plausible technical direction that still needs experiments, benchmarks, or formal proof.

## Decision Question 1 - What Problems Does DUUMBI Solve Today?

- Fact: Local PRD material positions DUUMBI around intent, semantic structure, executable code, runtime feedback, agent activity, and knowledge updates.
- Fact: The source repository and README describe DUUMBI as an AI-first semantic graph compiler with JSON-LD as the semantic graph representation and Cranelift as the native-code backend.
- Fact: Local roadmap and archive material indicate delivered work around graph compilation, module registry, import resolution, ownership/type validation, runtime support, agents, MCP tools, context assembly, and Studio workflows.
- Fact: The query-mode spec and source files show an emerging read-only query surface.
- Interpretation: DUUMBI already answers the problem of graph-native AI mutation better than a plain coding-agent workflow because semantic graph nodes, ownership constraints, registry metadata, and validation gates are first-class artifacts.
- Interpretation: DUUMBI already answers intent-driven development at the workflow level: user intent can become structured graph changes, generated code, tests, and evidence rather than only a conversational patch.
- Interpretation: DUUMBI partially answers deterministic behavior: graph parsing, validation, ownership/type checks, semantic hashes, dependency resolution, and compiled artifacts can be deterministic; LLM planning and generation remain probabilistic unless constrained by validation, replay, locked inputs, and evidence checks.
- Interpretation: DUUMBI already answers dynamic skill and agent construction at a practical orchestration layer through agent configs, provider abstraction, MCP tools, and context bundles.
- Interpretation: DUUMBI already answers low-token operation in part through context selection, token budgets, semantic context assembly, registry reuse, and compact graph facts instead of repeatedly pasting full source files.
- Interpretation: DUUMBI already answers semantic graph repository reuse in part through module packages, registry metadata, semantic hashing, dependency manifests, and cacheable graph modules.
- Interpretation: DUUMBI only partially answers large-system specification from `init`: project initialization exists, but the evidence does not yet show mature large-system bootstrapping from architecture intent, domain model, service boundaries, policies, tests, and deployment targets.
- Interpretation: DUUMBI only partially answers cross-platform builds: Unix-like developer workflows and native backend work exist, but Windows support should not be treated as delivered until Phase 16 CI, docs, and user-facing setup evidence are complete.
- Research hypothesis: DUUMBI can eventually answer Dijkstra-style correctness and formal verification more directly than text-first coding agents if verification conditions can be generated from the JSON-LD graph before code generation.

## Decision Question 2 - Which Claims Are Delivered, Partial, Planned, Or Research?

| Claim area                            |    Status | Claim label         | Evidence                                                                                    | Decision note                                                                                                                                                                                                                                   |
| ------------------------------------- | --------: | ------------------- | ------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Semantic graph compiler               | Delivered | Fact                | README, public docs, source repo compiler paths, local roadmap                              | Keep positioning this as the technical base, but avoid making it the whole product story.                                                                                                                                                       |
| Intent pipeline                       | Delivered | Fact                | PRD, roadmap, Phase 7/9a/10/12 archives, source repo agent/context modules                  | Present as an intent-to-validated-software path.                                                                                                                                                                                                |
| Registry and semantic hash reuse      | Delivered | Fact                | PRD, registry notes, roadmap/archive docs, source repo registry and package behavior        | Position as a reuse and determinism foundation, not only dependency management.                                                                                                                                                                 |
| Ownership and type validation         | Delivered | Fact                | PRD, Phase 10/12/15 archive evidence, source repo validation behavior                       | Treat as a core safety gate for AI-generated graph changes.                                                                                                                                                                                     |
| Dynamic agents, skills, and MCP       | Delivered | Fact                | PRD, Agentic Development Map, source repo agent/MCP support, MCP primary spec               | Treat as agent substrate capability, with tools constrained by graph validation.                                                                                                                                                                |
| Context and knowledge foundation      | Delivered | Fact                | PRD, context pack docs, vault maps, source repo context assembly                            | Use as evidence for lower-token operation and answerability.                                                                                                                                                                                    |
| Deterministic behavior                |   Partial | Interpretation      | Compiler/validator/cache evidence plus probabilistic agent layer                            | State the boundary clearly: deterministic substrate, not fully deterministic AI behavior.                                                                                                                                                       |
| Studio E2E workflow                   |   Partial | Fact                | Phase 15/15i archive docs and current PRD                                                   | Finish evidence before making strong public claims.                                                                                                                                                                                             |
| Query mode and public docs alignment  | Delivered | Fact                | Merged PR #565, `docs/modes/query-mode-spec.md`, README, sites docs source, CLI/Studio copy | Query is now exposed as a first-class read-only surface; future answer-schema hardening remains separate.                                                                                                                                       |
| Cross-platform support                |   Partial | Fact                | Phase 16 archive docs and repo status                                                       | Do not claim Windows support as mature until CI and docs prove it.                                                                                                                                                                              |
| Large-system `init` bootstrapping     |   Partial | Interpretation      | `init` project scaffolding exists; architecture-scale specification evidence is incomplete  | Define a separate service capability for large-system specification packages.                                                                                                                                                                   |
| Runtime failure feedback              |   Partial | Fact                | PRD, #580 product and technical specs, merged PR #604, Stage 12 closure evidence            | Local traced build/run foundation exists for trace/crash artifacts, function/block back-mapping, inspection, repair context, and validation evidence contracts. Human-reviewed repair proposals and production self-healing remain future work. |
| Production customer self-healing      |  Research | Assumption          | Phase 13 archive ambition plus missing account/cloud/update/privacy design                  | Do not plan as first slice; requires consent, telemetry ingestion, account identity, release delivery, rollback, and operational safety.                                                                                                        |
| Windows CI and docs                   |   Planned | Fact                | Phase 16 roadmap/archive direction                                                          | Prioritize reproducible developer onboarding across operating systems.                                                                                                                                                                          |
| Shared session and event architecture |   Planned | Interpretation      | PRD and Studio/agent/session direction                                                      | Needed for query, telemetry, and human-reviewable replay.                                                                                                                                                                                       |
| Dijkstra-style formal verification    |  Research | Research hypothesis | Local formal-verification ambition plus Dafny/verification literature                       | Test direct VCGen from DUUMBI graphs before claiming formal verification.                                                                                                                                                                       |
| Graph-based VCGen                     |  Research | Research hypothesis | JSON-LD graph representation and validation passes                                          | Evaluate against Dafny/Lean/Verus-style code or intermediate-language verification.                                                                                                                                                             |
| Compositional verification            |  Research | Research hypothesis | Module registry, ownership boundaries, semantic hashes                                      | Module packages may become proof boundaries if graph contracts are precise enough.                                                                                                                                                              |
| Code import                           |  Research | Research hypothesis | Current graph compiler direction and source-code ecosystem needs                            | Required if DUUMBI should reuse existing repositories, not only DUUMBI-native modules.                                                                                                                                                          |
| Semantic graph similarity and reuse   |  Research | Research hypothesis | Registry, semantic hashes, graph facts, external graph retrieval systems                    | Degree storage, embeddings, and graph matching need benchmarked retrieval quality.                                                                                                                                                              |
|                                       |           |                     |                                                                                             |                                                                                                                                                                                                                                                 |

## Decision Question 3 - What Should The Service Answer When A User Asks Questions?

- Interpretation: The service should answer read-only questions before it offers write-capable mutation.
- Interpretation: The first service surface should be `query`, available through CLI, Studio, README examples, and public docs.
- Fact: The target answer shape already exists in the PRD vocabulary: intent, graph, source location, agent activity, evidence, runtime feedback, and knowledge updates.
- Interpretation: A DUUMBI answer should include the claim label, source artifact, graph node or symbol when available, confidence, missing evidence, and recommended next action.

- Interpretation: The read-only answer contract should cover these question types:

| User question | Claim label | Expected DUUMBI answer |
|---|---|---|
| What exists? | Interpretation | List graph nodes, modules, intents, packages, source files, docs, and tests that represent the requested capability. |
| Why does it exist? | Interpretation | Link intent records, PRD sections, roadmap decisions, archive notes, issues, or commits that justify the behavior. |
| Where does behavior live? | Fact | Return semantic graph node IDs, module paths, generated source paths, tests, runtime traces, and docs. |
| What depends on it? | Fact or Interpretation | Return graph edges, module dependencies, registry package consumers, generated-code references, and knowledge links. |
| What evidence proves it? | Fact | Return tests, benchmark output, CI state, archive evidence, telemetry, or manual review notes. |
| What risk does a change carry? | Interpretation | Return affected graph subgraphs, ownership/type boundaries, test coverage gaps, runtime traces, and human-review checkpoints. |
| Is behavior deterministic? | Interpretation | Separate deterministic compiler/validator/cache behavior from probabilistic agent behavior and show replay/evidence status. |
| Can this be formally verified? | Research hypothesis | State whether current graph facts are enough for VCGen, what contracts are missing, and what proof target would be used. |
| Can DUUMBI reuse an existing module? | Interpretation | Query semantic hashes, registry metadata, graph similarity, ownership constraints, and compatibility evidence. |
| How much context will this use? | Interpretation | Estimate token budget, selected context packs, graph summaries, omitted files, and fallback expansion steps. |
| Can this run on this platform? | Fact or Interpretation | Return supported build targets, missing Windows or OS-specific evidence, CI results, and setup docs. |
| Can `init` specify a large system? | Interpretation | Return supported scaffolding today, missing architecture-spec inputs, and the next validation path. |
| Can DUUMBI help diagnose this runtime crash? | Interpretation | For local Phase 13 flows, return trace/crash artifacts, mapped graph function/block or node context, repair readiness, validation gates, and missing evidence. |

## Decision Question 4 - Where Is DUUMBI Compared With The Market?

- Fact: GitHub Copilot coding agent is positioned as an agent that can be assigned work, operate in a development environment, push branches, and open pull requests.
- Fact: MCP standardizes server-exposed tools and related context surfaces for model clients.
- Fact: Codex skills package task-specific instructions, resources, and scripts so an agent can reliably follow a workflow.
- Fact: Anthropic's agent guidance distinguishes workflows with predefined code paths from agents that dynamically direct tool use and process control.
- Fact: CodeQL represents code as queryable data for semantic code analysis.
- Fact: SCIP defines an index format for code intelligence across languages and tools.
- Fact: GraphRAG uses graph-based retrieval over connected knowledge when plain vector retrieval is not enough.
- Fact: Bazel remote caching and Dagger both show market demand for reproducible, cacheable, inspectable build and workflow execution.
- Interpretation: Most market systems optimize either coding-agent autonomy, tool interop, code search, graph retrieval, or reproducible builds.
- Interpretation: DUUMBI's differentiated position is the combination: semantic graph as source artifact, deterministic validation gates, semantic hashes, module registry reuse, native compilation, agent orchestration, and evidence-bearing workflow.
- Interpretation: DUUMBI should not be positioned as another coding agent.
- Interpretation: DUUMBI should be positioned as the semantic execution substrate that coding agents use when behavior must be inspectable, reusable, testable, and eventually formally verifiable.
- Assumption: The market will require visible proof in the form of working demos, queryable evidence, and cross-platform setup before this distinction is credible.

## Decision Question 5 - What Should Be Built Next?

- Fact: Near-term product work has made read-only question answering visible as a first-class service surface in README, repo docs, public docs source, CLI/TUI copy, and Studio copy via merged PR #565.
- Fact: PR #565 updated README, repo docs, sites docs source, CLI/TUI copy, and Studio copy so Query appears before write-capable Agent/Intent workflows. It also removed stale `duumbi viz` documentation because that command is not a supported or planned surface.
- Interpretation: The immediate build order should be:
  1. Keep Query-first exposure accurate across CLI help, Studio, README, and public docs as surfaces evolve.
  2. Define the standard answer schema: claim label, source, nodeId/symbol, evidence, dependency impact, risk, and next action.
  3. Finish Phase 15/15i evidence so the end-to-end Studio workflow can be shown without relying on archive prose.
  4. Complete Phase 16 Windows CI, setup docs, and platform compatibility checks.
  5. Extend Phase 13 runtime failure feedback from the local trace/back-mapping foundation toward human-reviewable repair proposals without crossing into autonomous repair acceptance.
  6. Add a shared session/event architecture that can support query replay, agent execution, telemetry, and review.
  7. Extend `init` from project scaffolding to large-system specification packages: architecture intent, domain model, module boundaries, policy constraints, test plan, build targets, and deployment assumptions.
  8. Turn semantic graph repository reuse into a queryable recommendation surface: existing module, semantic hash, compatibility reason, confidence, and risk.
- Assumption: The first public service promise should be conservative: "ask DUUMBI what exists, why it exists, where it lives, what proves it, and what change risk exists."
- Assumption: Write-capable repair and generation should remain behind validation and human-reviewable diffs until telemetry evidence is strong.
- Assumption: Customer-production self-healing should remain out of scope until local developer/test failure feedback is proven and product specs cover privacy, account identity, upload, delivery, rollback, and user consent.

## Decision Question 6 - What Should Be Researched?

- Research hypothesis: Direct VCGen from DUUMBI JSON-LD graphs may be simpler and more reliable than generating verified code from natural language or from a general-purpose intermediate language.
- Research hypothesis: Dijkstra-style weakest-precondition reasoning can map cleanly to DUUMBI graph nodes if effects, state transitions, preconditions, postconditions, ownership boundaries, and error paths are explicit in the graph.
- Research hypothesis: Compositional verification can use registry module boundaries and semantic hashes as proof cache boundaries.
- Research hypothesis: Graph degree, centrality, type signatures, semantic hashes, and behavioral summaries can improve repository reuse beyond text search or embedding-only retrieval.
- Research hypothesis: Token usage can be reduced further by storing stable graph facts, context packs, answer traces, and proof obligations rather than reconstructing context from raw source on every task.
- Research hypothesis: Dynamic skill and agent selection can be made safer if tools are selected from graph-visible capability needs and validated against ownership, type, and evidence policies.
- Research hypothesis: Large-system `init` can become a specification compiler if it accepts architecture-level constraints and generates graph modules, policies, test targets, CI, docs, and queryable intent records.
- Research hypothesis: Cross-platform native builds can be made more reliable if build targets are represented as graph facts and validated before code generation.

- Interpretation: Research experiments worth running:

| Research topic | Claim label | Experiment |
|---|---|---|
| Dijkstra/formal verification | Research hypothesis | Generate verification conditions directly from a small DUUMBI graph and prove them with an SMT solver; compare effort against Dafny/Verus-style code-first verification. |
| Determinism | Research hypothesis | Replay the same intent with locked model, prompt, context pack, graph input, and registry state; measure graph diff stability and validation pass/fail variance. |
| Semantic reuse | Research hypothesis | Build a module-reuse benchmark where retrieval uses text only, embeddings only, semantic hashes, and graph features; compare precision and edit distance. |
| Token usage | Research hypothesis | Measure task completion quality and token budget using full source context, selected source context, graph summaries, and graph+evidence answer traces. |
| Dynamic agents | Research hypothesis | Compare static agent routing with graph-driven tool/skill selection and score validation failure rate, cost, and review time. |
| Large-system `init` | Research hypothesis | Define one realistic multi-service system from an architecture intent file and measure generated graph completeness, missing decisions, and test coverage. |
| Cross-platform builds | Research hypothesis | Run the same graph module across macOS, Linux, Windows, and optionally WASM targets; compare generated artifacts, runtime behavior, and failure diagnostics. |

## Explicit Topic Answers

- Determinism:
  - Fact: DUUMBI has deterministic artifacts such as graph inputs, validation passes, semantic hashes, registry metadata, and compiled outputs.
  - Interpretation: DUUMBI should claim deterministic substrate behavior, not fully deterministic end-to-end AI behavior.
  - Research hypothesis: Replayable sessions and locked context can narrow the nondeterministic surface enough for audited agent execution.
- Dijkstra and formal verification:
  - Fact: Formal verification is not delivered as a production capability.
  - Interpretation: DUUMBI is structurally well positioned for formal verification because behavior is represented as explicit semantic graph facts before code generation.
  - Research hypothesis: Direct graph-based VCGen can make weakest-precondition and compositional verification easier than verifying generated source text.
- Dynamic skills and agents:
  - Fact: DUUMBI has agent/provider/MCP/context foundations.
  - Interpretation: The service should expose why a skill or tool was selected and what graph evidence justified it.
- Token usage:
  - Fact: DUUMBI has context assembly and registry/graph reuse foundations.
  - Interpretation: Low-token use is a partial capability until measured with repeatable benchmarks and public examples.
- Semantic graph repository reuse:
  - Fact: DUUMBI has module registry, package metadata, semantic hash, and dependency foundations.
  - Interpretation: Repository-level knowledge reuse should become an explicit query answer and recommendation surface.
  - Research hypothesis: Graph degree and graph similarity can improve reuse ranking if evaluated against real repositories.
- Large-system `init`:
  - Fact: Project initialization exists.
  - Interpretation: Large-system specification from `init` is not yet mature.
  - Assumption: The right next step is an architecture-intent input format that generates graph modules, boundaries, policies, docs, tests, and build targets.
- Cross-platform builds:
  - Fact: Cross-platform support is not mature until Windows CI, setup docs, and target-specific validation are complete.
  - Interpretation: Cross-platform should be treated as a Phase 16 delivery track, not a completed service promise.
- Runtime failure feedback and self-healing:
  - Fact: Phase 13 archive material defines telemetry and self-healing around runtime errors, trace back-mapping, repair patches, tests, and human-reviewable diffs.
  - Fact: Issue #580 and implementation PR #604 delivered the first local foundation: opt-in traced builds, trace/crash artifacts, graph function/block back-mapping, telemetry inspection, repair crash context, and repair validation evidence contracts.
  - Fact: Issue #583 and implementation PR #614 narrowed the local trace surface by validating traced-build telemetry configuration for sampling, sample rate, artifact directories, environment overrides, and value-capture boundaries while preserving default-off build behavior.
  - Interpretation: The first productized use case should be local developer/test failure diagnosis and repair evidence, because it avoids cloud ingestion, customer privacy, release delivery, and auto-update risk while proving the core technical loop.
  - Assumption: Production customer crash collection and automatic repair delivery should be specified separately after the local/CI flow works.

## Source Set

- Fact: Local sources reviewed or targeted:

- Fact: [[DUUMBI - PRD]]
- Fact: [[Runtime Failure Feedback Loop]]
- Fact: [[DUUMBI Roadmap Map]]
- Fact: `docs/modes/query-mode-spec.md` in the source repository.
- Fact: `README.md` in the source repository.
- Fact: Phase 7 archive docs on agentic workflows and intent execution.
- Fact: Phase 9a archive docs on local development pipeline and provider abstraction.
- Fact: Phase 10 archive docs on ownership, lifetimes, and mutation safety.
- Fact: Phase 12 archive docs on registry, validation, and package reuse.
- Fact: Phase 13 archive docs on telemetry and repair direction.
- Fact: Issue #580, product spec PR #600, technical spec PR #602, implementation PR #604, and Stage 12 closure evidence for the local telemetry/back-mapping foundation.
- Fact: Issue #583, product spec PR #609, technical spec PR #611, implementation PR #614, Stage 11 review artifact, and Stage 12 closure evidence for traced-build telemetry configuration validation.
- Fact: Phase 15 and 15i archive docs on Studio E2E and end-to-end evidence.
- Fact: Phase 16 archive docs on Windows and platform support.
- Fact: Public DUUMBI docs in the source repository.

- Fact: External primary/current sources:

- Fact: [GitHub Copilot coding agent](https://docs.github.com/copilot/concepts/coding-agent/about-copilot-coding-agent)
- Fact: [MCP tools specification](https://modelcontextprotocol.io/specification/2025-06-18/server/tools)
- Fact: [OpenAI Codex skills](https://developers.openai.com/codex/skills)
- Fact: [Anthropic - Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)
- Fact: [CodeQL overview](https://codeql.github.com/docs/codeql-overview/about-codeql/)
- Fact: [SCIP](https://scip-code.org/)
- Fact: [Microsoft GraphRAG](https://www.microsoft.com/en-us/research/project/graphrag/)
- Fact: [Vericoding benchmark](https://arxiv.org/abs/2509.22908)
- Fact: [Dafny as verification-aware intermediate language](https://arxiv.org/abs/2501.06283)
- Fact: [Dafny compositional verification](https://arxiv.org/abs/2509.23061)
- Fact: [Bazel remote caching](https://bazel.build/remote/caching)
- Fact: [Dagger docs](https://docs.dagger.io/)

## Decision

- Interpretation: The next public-facing product story should lead with question answering and evidence, then show mutation and compilation as the write path.
- Interpretation: The internal roadmap should treat `query` as the first service UX for DUUMBI-as-a-service.
- Interpretation: The strategic moat is not "AI writes code"; it is "AI changes validated semantic software artifacts with reusable graph knowledge and inspectable evidence."
- Research hypothesis: Formal verification becomes credible only after graph contracts, effect modeling, and VCGen experiments prove that DUUMBI graphs are a better verification source than generated code.

## Related

- [[DUUMBI - PRD]]
- [[DUUMBI Core Concepts Map]]
- [[DUUMBI Technical Architecture Map]]
- [[DUUMBI Agentic Development Map]]
