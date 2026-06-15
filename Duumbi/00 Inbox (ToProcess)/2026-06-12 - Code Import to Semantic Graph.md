---
tags:
  - duumbi/inbox/enriched
  - duumbi/status/processed
  - duumbi/classification/research
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/high
duumbi_inbox_enrichment: processed
duumbi_inbox_enrichment_generated_at: 2026-06-15T07:59:27.698Z
---

# Code Import to Semantic Graph

<!-- duumbi-inbox-enrichment:v1 status=processed generated_at=2026-06-15T07:59:27.698Z -->

## Source
- Surface: Manual Obsidian edit
- Vault path: Duumbi/00 Inbox (ToProcess)/2026-06-12 - Code Import to Semantic Graph.md
- Submitted by: unknown unless explicit in the raw input

## Raw input
> ---
> tags:
>   - duumbi/inbox/roadmap
>   - duumbi/status/to-process
>   - duumbi/classification/research
>   - duumbi/value/high
>   - duumbi/importance/medium
>   - duumbi/complexity/high
> created: 2026-06-12
> milestone: M5
> source: "[[DUUMBI Future Development Roadmap Map]]"
> ---
> 
> # Code Import to Semantic Graph
> 
> ## Context
> 
> [[DUUMBI - Service and Research Direction]] lists code import as a research hypothesis: required if DUUMBI should reuse existing repositories, not only DUUMBI-native modules. Without import, DUUMBI only serves greenfield projects. The Loop plan's tree-sitter indexing pipeline (symbols, imports, call/reference edges, 9 languages) is a useful **shallow** layer, but true import means producing executable DUUMBI Op graphs, which is much harder.
> 
> ## Goal
> 
> A two-tier import story: (Tier A) shallow knowledge import — index existing codebases into queryable knowledge graphs (Loop-style, tree-sitter) so `query` works over legacy code; (Tier B) deep import — translate selected functions into executable DUUMBI Op graphs with verified behavioral equivalence.
> 
> ## Subtasks
> 
> 1. Scope decision: which tier serves which product (Tier A → Loop knowledge base + query; Tier B → reuse/migration). Confirm Tier A ships first.
> 2. Tier A: adopt/build the tree-sitter pipeline from the Loop plan (`codegraph-parser`), emitting knowledge-graph JSON-LD (distinct vocabulary from executable Op graphs); store in the graph-aware `duumbi-registry`.
> 3. Tier B research spike: pick one source language subset (suggestion: simple Rust or Python functions over ints/strings/arrays) → lift to Op graph via AI translation + test-based equivalence checking (run original vs. DUUMBI build on generated inputs).
> 4. Equivalence evidence: imported function ships with the test corpus proving behavioral match; mark imported graphs' provenance.
> 5. Define the boundary honestly in docs: imported knowledge (queryable) vs. imported behavior (executable) — avoid overclaiming.
> 6. Benchmark: import a real small library and measure how much becomes executable vs. knowledge-only.
> 
> ## Acceptance criteria
> 
> - Tier A: `duumbi knowledge`/`query` answers structural questions over a non-DUUMBI repo.
> - Tier B: ≥10 real functions imported to executable graphs with passing equivalence tests.
> - Research note with the measured feasibility of deep import.
> 
> ## Links
> 
> - [[DUUMBI Future Development Roadmap Map]]
> - [[2026-06-12 - Registry Graph Database Evolution]]
> - [[2026-06-12 - Semantic Graph Similarity and Reuse]]
> - [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]]

## Interpreted intent

Implement a two-tier code import system so DUUMBI can reuse existing codebases. Tier A: shallow knowledge import via tree-sitter indexing for queryable structural knowledge. Tier B: deep import translating selected functions into executable DUUMBI Op graphs with behavioral equivalence evidence.

## Developer summary

Add ability to import external code repositories into DUUMBI's semantic graph. Tier A (shallow): use tree-sitter to parse source code into a knowledge graph, storing it in the graph-aware registry so `duumbi query` can answer structural questions over non-DUUMBI code. Tier B (deep): research spike to lift selected functions into compilable DUUMBI Op graphs via AI-assisted translation and behavioral equivalence tests (run original vs. DUUMBI build on generated inputs). Ship Tier A first; Tier B is a research milestone. Coordinate with the Loop plan's tree-sitter pipeline and Registry Graph Database Evolution.

## UML overview

```mermaid
flowchart TD
    A[External Codebase] --> B[tree-sitter Parser]
    B --> C[Knowledge Graph (Tier A)]
    C --> D[duumbi-registry]
    D --> E[query / knowledge CLI]

    A --> F[Selected Functions]
    F --> G[AI-Assisted Translation]
    G --> H[DUUMBI Op Graph (Tier B)]
    H --> I[Equivalence Test Runner]
    I -->|pass/fail| J[Evidence Ledger]

    subgraph Tier A shallow knowledge import
        B
        C
        D
        E
    end

    subgraph Tier B deep import research spike
        F
        G
        H
        I
        J
    end
```

## Classification
- Type: research
- Business value: high
- Importance: medium
- Complexity: high

## Clarifications
### Answered
- Tier A ships first; Tier B is a research spike with a separate acceptance criteria (≥10 real functions).
- The shallow pipeline will use tree-sitter and output JSON-LD knowledge graphs distinct from executable Op graphs.
- Equivalence evidence will be test-based (run original and DUUMBI builds on generated inputs and compare behavior).
- The import feature is a hypothesis from the DUUMBI - Service and Research Direction document.

### Open
- Which source language subset should Tier B initially target? (Suggestion: simple Rust or Python functions over ints/strings/arrays.)
- What exact vocabulary and schema will the Tier A knowledge graph use?
- How will equivalence tests handle external dependencies and non-determinism?
- Should deep-imported functions retain source-language semantics (e.g., mutable borrows) or be translated to a DUUMBI-idiomatic form?
- What is the strategy for updating imported knowledge when the external codebase changes?
- Which specific tree-sitter grammars (out of the 9 languages in the Loop plan) should be supported in Tier A v0?
- Should Tier A knowledge graphs be queryable via the same `query` CLI as native DUUMBI graphs, or through a separate interface?
- What is the performance target for querying a large imported knowledge graph (e.g., latency, graph size)?

## Relevant DUUMBI context
- [[DUUMBI - Service and Research Direction]] – provides the hypothesis that import is required for non-greenfield use; references the Loop plan's tree-sitter pipeline.
- [[DUUMBI Future Development Roadmap Map]] – places this note under milestone M5 and connects it to other roadmap items.
- [[2026-06-12 - Registry Graph Database Evolution]] – dependency for storing both knowledge and executable graphs with semantic hashing.
- [[2026-06-12 - Semantic Graph Similarity and Reuse]] – related concept for detecting reusable functions across imports.
- [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]] – the Loop plan that contains the tree-sitter indexing pipeline design.
- src/graph/mod.rs – defines the graph IR and edge types needed to represent knowledge-graph relationships.
- src/registry/ – the registry client that will need to handle graph-aware storage for imported knowledge graphs.
- src/types.rs – defines NodeId, GraphNode, and Op types that Tier B deep import must produce.

## Related GitHub context

No known GitHub issues or PRs directly reference code import or external tree-sitter indexing. The Loop plan (DUUMBI Loop Native Workflow Adaptation) may have a related issue. Triage should verify if an issue for 'code indexing' or 'import' exists, and check the duumbi-loop repository for tree-sitter infrastructure.

## Initial routing recommendation

GitHub issue

## Requested follow-up
- Create a GitHub issue for 'Code Import to Semantic Graph' with the acceptance criteria from this Inbox note.
- During Stage 4 triage, confirm no duplicate exists and link the issue to milestone M5.
- After human acceptance (Stage 5), produce a combined product and technical spec that separates Tier A (execution) and Tier B (research) into distinct implementation tasks.
- Coordinate with the owner of 'Registry Graph Database Evolution' on the knowledge graph storage contract.
- Ensure the spec defines the boundary between queryable knowledge (Tier A) and executable behavior (Tier B) to avoid overclaiming.
- Schedule a research spike for Tier B after Tier A is delivered, to measure deep-import feasibility with a real small library.

## AI agent instructions
- When creating the GitHub issue, title it exactly 'Code Import to Semantic Graph'.
- Use labels: 'research', 'high-value', 'complexity-high', 'milestone-M5'.
- In the issue body, summarize the two-tier goal, subtasks, and acceptance criteria from the Inbox note.
- Include the uml_diagram_mermaid in the issue as a visual aid.
- Reference the related vault notes (Service and Research Direction, Registry Graph Database Evolution, etc.) as background.
- Indicate that Tier A (shallow import) should be planned as a concrete feature with a defined subtree, while Tier B (deep import) is a research track with a separate acceptance spike.
- Do not design the knowledge graph vocabulary in the issue; note that it must be defined in the technical spec.
- Set the initial state to 'Todo' and assign to the triage board. After Stage 4 routing, it should move to 'Needs Human Acceptance'.

## Scope candidate
### In
- Tier A: adapt or build tree-sitter indexing pipeline from Loop plan, parsing source code into a JSON-LD knowledge graph with symbols, imports, and call/reference edges.
- Store Tier A knowledge graphs in the graph-aware `duumbi-registry` alongside native graphs.
- Expose structural queries over imported knowledge via `duumbi query` or similar CLI (e.g., find definitions, callers, use sites).
- Tier B research: design a pipeline to translate a selected subset of functions from one source language into DUUMBI Op graphs using AI, with equivalence testing via behavioral comparison.
- Benchmark deep import on a small real-world library and publish feasibility findings.
- Mark imported graphs with provenance metadata (source language, original source location, equivalence evidence).
- Define and document the honest boundary: imported knowledge (queryable) vs. imported behavior (executable).

### Out
- Full language support for all 9 Loop languages in the initial Tier A release; start with one or two (e.g., Rust and Python).
- Deep import (Tier B) for all loaded functions; research spike limited to simple, self-contained functions.
- Replacing native DUUMBI modules with imported equivalents in production workloads.
- Production readiness for deep import; Tier B is explicitly a research deliverable.
- Automated equivalence proof via formal verification; initial Tier B relies on test-based comparison only.

## Risks and trade-offs
- Deep import (Tier B) may prove infeasible for arbitrary, complex code; behavioral equivalence is hard to guarantee.
- Tree-sitter grammars add maintenance burden and may lag behind language evolution.
- The knowledge graph vocabulary might need to differ significantly from the executable Op graph schema, creating two separate but parallel graph models.
- Performance of large-scale knowledge graph queries in the registry is untested and may require indexing optimizations.
- Equivalence testing requires compiling and running external code, introducing toolchain dependencies and potential security concerns.
- If external code changes frequently, keeping imported knowledge up-to-date may require a continuous import pipeline, not just a one-time snapshot.
- The AI translation step in Tier B may generate non-idiomatic or inefficient Op graphs that pass tests but are hard to maintain or combine.

## Obsidian tags

#duumbi/inbox/enriched #duumbi/status/processed #duumbi/classification/research #duumbi/value/high #duumbi/importance/medium #duumbi/complexity/high

## Enrichment result
- Date: 2026-06-15T07:59:27.698Z
- Status: ready for triage
- Canonical duplicate: none verified
- Facts:
- The Inbox note was created manually in the Obsidian vault on 2026-06-12 under milestone M5.
- It is tagged as a research item with high value, medium importance, and high complexity.
- The note references the DUUMBI - Service and Research Direction document and several related roadmap notes.
- The note explicitly defines two tiers and a set of subtasks and acceptance criteria.
- No GitHub issue or PR currently exists for code import; triage must verify this.
- The Loop plan (DUUMBI Loop Native Workflow Adaptation) contains a tree-sitter indexing pipeline design that can be reused.
- DUUMBI's registry (duumbi-registry) is being evolved to handle graph-aware storage, which is a prerequisite for Tier A.
- Assumptions:
- The tree-sitter pipeline described in the Loop plan is sufficiently specify to be adapted for DUUMBI's knowledge graph output.
- The graph-aware registry (Registry Graph Database Evolution) will be ready to store large knowledge graphs by the time Tier A implementation starts.
- An AI-assisted translation from source code to DUUMBI Op graphs is possible with current LLM capabilities for simple functions.
- Behavioral equivalence can be sufficiently validated by generating inputs and comparing outputs without formal proof, at least for the research spike.
- The user or product owner has agreed that Tier A shipping first is acceptable and that Tier B can remain research-only until proven.
- The vocabulary for the knowledge graph will be defined separately from the executable Op graph schema to avoid semantic confusion.
- Recommendations:
- Route to Stage 4 triage and recommend acceptance as a GitHub issue.
- Split the work into two distinct tracks: a product feature (Tier A) and a research spike (Tier B) with separate acceptance criteria and deliverables.
- Coordinate early with the Loop plan owner to align on tree-sitter parser reuse and vocabulary.
- Prioritize Tier A to unlock `query` over legacy codebases, which expands DUUMBI's immediate utility.
- Defer Tier B deep-import commitment until after the research spike demonstrates feasibility; use the spike to inform whether to invest further or pivot to a different approach.
- In the technical spec for Tier A, define the knowledge graph schema, query API, and integration points with the registry and CLI.
- Ensure the issue is assigned to milestone M5 and linked to the Registry Graph Database Evolution and Semantic Graph Similarity and Reuse notes.
