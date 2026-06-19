---
tags:
  - duumbi/inbox/enriched
  - duumbi/status/processed
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/medium
duumbi_inbox_enrichment: processed
duumbi_inbox_enrichment_generated_at: 2026-06-19T18:37:22.033Z
---

# Intent Create Hardening

<!-- duumbi-inbox-enrichment:v1 status=processed generated_at=2026-06-19T18:37:22.033Z -->

## Source
- Surface: Manual Obsidian edit
- Vault path: Duumbi/00 Inbox (ToProcess)/2026-06-12 - Intent Create Hardening.md
- Submitted by: unknown unless explicit in the raw input

## Raw input
> ---
> tags:
>   - duumbi/inbox/roadmap
>   - duumbi/status/to-process
>   - duumbi/classification/execution
>   - duumbi/value/critical
>   - duumbi/importance/high
>   - duumbi/complexity/medium
> created: 2026-06-12
> milestone: M1
> source: "[[DUUMBI Future Development Roadmap Map]]"
> ---
> 
> # Intent Create Hardening
> 
> ## Context
> 
> User experience: intent create often fails and is slow, and not all models can do it. The code confirms why — create is the least-defended step in the pipeline (verified 2026-06-12):
> 
> - **Single LLM call, no retry** (`src/intent/create.rs`) — while execute has a 5-retry escalating ladder, create gets one shot.
> - **Free-text JSON output, no schema enforcement** — the model must emit valid JSON inside plain text (`call_plain_completion`); weak/mid models fail exactly here. Parse failure falls back to only **3 hardcoded benchmark specs** (calculator, string-utils, math-library) — everything else just fails.
> - **Zero workspace context** — the Phase 10 context pipeline (classify → traverse → collect → budget → few-shot) is used for mutations but NOT for create; the spec is generated blind to existing modules, past intents, or learned examples.
> - **No streaming, optional clarification adds a second round-trip** — perceived slowness with no progress feedback.
> 
> ## Goal
> 
> Intent create succeeds reliably on mid-tier models, recovers from malformed output instead of failing, sees the workspace it is planning for, and streams visible progress.
> 
> ## Subtasks
> 
> 1. Schema-constrained spec output: use the tool-use / structured-output path for the spec JSON where the provider supports it (capability-gated via [[2026-06-12 - Model Capability Advisor and Task Routing]]); free-text JSON remains only as fallback for providers without it.
> 2. Retry-with-feedback ladder for create: ≥2 retries where parse/validation errors are returned to the model field-by-field (mirror the execute ladder), instead of one-shot fail.
> 3. Context enrichment for create: workspace module summary, active intents (duplicate/conflict detection), and top-N similar archived intents from the learning store as few-shot templates — the same machinery mutations already use.
> 4. Generalize the fallback: replace the 3 hardcoded benchmarks with retrieval of similar archived/published intent specs as templates (local learning store now, registry similarity later).
> 5. Streaming + phase progress: stream spec generation; show clarify/spec phases in TUI; route the clarification pass to a cheap fast model (advisor decides) or skip it when the description is information-rich.
> 6. Create-specific eval in `phase15-e2e`: per provider/model success rate for intent create itself (today evals cover the whole flow); results feed the advisor's adequacy data and the model catalog evidence.
> 7. Track create latency (p50/p95) and success rate in session stats as standing metrics.
> 
> ## Acceptance criteria
> 
> - Create success rate on the eval corpus measurably improves on mid-tier models (baseline measured first; target ≥80% where the same model previously failed).
> - A malformed spec response is repaired automatically at least once before user-visible failure, with the error shown.
> - Created specs reference existing workspace modules correctly (no blind duplicates).
> - User sees streamed progress; clarify+create p95 latency is measured and reported.
> 
> ## Links
> 
> - [[DUUMBI Future Development Roadmap Map]]
> - [[2026-06-12 - Model Capability Advisor and Task Routing]]
> - [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
> - [[2026-06-12 - Token-Efficient Graph Representation for LLM IO]]

## Interpreted intent

Hardening the intent creation pipeline to make it reliable on mid-tier models, recover from malformed output, see workspace context, and stream visible progress.

## Developer summary

Currently `intent create` fails often on mid-tier models because it has no retry, no schema enforcement, and no workspace context. This hardening adds: (1) gated structured output using the Model Capability Advisor, (2) a retry ladder (≥2), (3) injection of workspace module summaries, active intents, and few-shot templates, (4) generalized fallback via retrieval instead of 3 hardcoded benchmarks, (5) streaming progress with phase visualization, (6) create-specific eval in phase15-e2e, and (7) latency/success metrics. The goal is a measurable success rate improvement with automated repair before user-visible failure.

## UML overview

```mermaid
sequenceDiagram
    participant User
    participant CLI/TUI
    participant Advisor as Model Capability Advisor
    participant Workspace as Workspace Context
    participant LLM
    User->>CLI/TUI: intent create <description>
    CLI/TUI->>Advisor: can use structured output?
    Advisor-->>CLI/TUI: yes (tool-use) or no (fallback JSON)
    CLI/TUI->>Workspace: collect context (modules, intents, few-shot)
    Workspace-->>CLI/TUI: context
    loop retry ladder (max 2)
        CLI/TUI->>LLM: generate spec with context (streaming)
        LLM-->>CLI/TUI: spec JSON (partial stream)
        CLI/TUI->>CLI/TUI: validate parse & fields
        alt parse/validation fails
            CLI/TUI->>LLM: feedback with field errors, retry
        else success
            break
        end
    end
    CLI/TUI-->>User: stream progress & final spec
    Note over CLI/TUI: if retries exhausted, fallback to generalized retrieval
```

## Classification
- Type: execution
- Business value: critical
- Importance: high
- Complexity: medium

## Clarifications
### Answered
- Current create flaws and target improvements are clearly listed in the note.
- Subtask list, acceptance criteria, and milestones are explicit.
- The note states it depends on the Model Capability Advisor (in progress).
- Context enrichment should mirror existing mutation pipeline (classify, traverse, etc.)
- Streaming should show clarify/spec phases in TUI.

### Open
- What should be the exact retry count and backoff pattern? Mirror execute (5) or a lighter ladder?
- Which specific mid-tier models must the hardening target?
- Should streaming use the same model or a separate fast model?
- At what description length or info richness should the clarification pass be skipped?
- How will generalized fallback retrieve similar specs from the learning store? Vector similarity? Keyword?
- Should the eval test only local providers or also OpenRouter models?
- How will workspace context token budget be managed? (e.g., if many intents exist)
- Will the TUI require significant refactoring to show per-phase progress?

## Relevant DUUMBI context
- Vault: [[DUUMBI Future Development Roadmap Map]] – intent create hardening is a milestone M1 item.
- Vault: [[2026-06-12 - Model Capability Advisor and Task Routing]] – the advisor must gate structured output.
- Vault: [[2026-06-12 - Intent at Scale Multi-Module and BDD]] – related to multi-module intents.
- Vault: [[2026-06-12 - Token-Efficient Graph Representation for LLM IO]] – may help token-efficient few-shot templates.
- Source: src/intent/create.rs – current single-call, free-text JSON create with hardcoded fallback.
- Source: src/agents/orchestrator.rs – execute's 5-retry ladder pattern for reuse.
- Source: src/agents/mod.rs – provider trait, structured output capabilities.
- Source: src/agents/prompts.rs – context injection templates.
- Source: .duumbi/intents/ – workspace intents for context.
- Source: tests/e2e/phase15-e2e.rs – where create-specific eval should be added.
- Doc: DUUMBI - Agentic Development Runbook – streaming and progress UX patterns.

## Related GitHub context

No known related GitHub issues; triage should verify existing intent create issues (if any).

## Initial routing recommendation

GitHub issue

## Requested follow-up
- Measure baseline intent create success rate per provider/model.
- Confirm Model Capability Advisor integration status and APIs.
- Design retry ladder parameters (count, backoff, feedback format).
- Select target mid-tier models for hardening.
- Decide streaming implementation (same model vs fast model).
- Build create-specific eval harness in phase15-e2e.
- Implement and iterate until acceptance criteria met.

## AI agent instructions
- Create a GitHub issue with title 'Intent Create Hardening' and the full description including the developer summary, UML diagram, subtasks, acceptance criteria, and risks.
- Link the issue to the relevant vault notes (Model Capability Advisor, etc.) and source files (create.rs, orchestrator.rs).
- Add labels: 'intent', 'improve-performance', 'hardening', possibly 'dependencies' (for advisor).
- Set milestone to M1 and assign priority high.
- Do not start implementation; only generate the issue for human triage in Stage 4.

## Scope candidate
### In
- Structured output path (tool-use) gated by capability advisor.
- Retry ladder with at least 2 retries and field-level feedback.
- Workspace context injection: module summaries, active intents, few-shot from learning store.
- Generalized fallback: replace hardcoded 3 benchmarks with similarity retrieval from learning store.
- Streaming spec generation with phase progress in TUI.
- Optional cheap clarification pass via fast model when description is poor.
- Create-specific eval in phase15-e2e.
- Track create latency (p50/p95) and success rate as standing metrics.

### Out
- Changes to the intent execute pipeline.
- Refactoring of the entire intent workflow architecture.
- Adding new intent types or major new UI surfaces beyond TUI progress.
- Generalization of the fallback beyond local learning store (registry similarity is future work).

## Risks and trade-offs
- Structured output support may be limited to few providers/models; fallback might still fail if retrieval quality is low.
- Retry loop increases latency and LLM cost; need to balance reliability with user wait time.
- Workspace context could exceed token budgets for large codebases; must implement token-efficient packing.
- Streaming progress with retries may confuse users if interrupted; UX design must handle partial output gracefully.
- Create-specific eval may require significant work to set up, increasing initial implementation cost.
- Dependency on Model Capability Advisor: if not ready, structured output gating will be delayed.

## Obsidian tags

#duumbi/inbox/enriched #duumbi/status/processed #duumbi/classification/execution #duumbi/value/critical #duumbi/importance/high #duumbi/complexity/medium

## Enrichment result
- Date: 2026-06-19T18:37:22.033Z
- Status: ready for triage
- Canonical duplicate: none verified
- Facts:
- Current create: single LLM call, free-text JSON, no retry; fallback is 3 hardcoded benchmarks.
- Execute pipeline already has a 5-retry ladder, tool-use for structured output, and streaming.
- Phase 10 context pipeline (classify/traverse/collect/budget/few-shot) exists and is used for mutations.
- Learning store records successful intent specs, but create does not use them for few-shot.
- Model Capability Advisor is being developed in a parallel Inbox note (2026-06-12) and will be needed.
- Streaming technology (ratatui, indicatif) is present in the codebase.
- Acceptance criteria mention baseline measurement and ≥80% success target.
- Assumptions:
- The Model Capability Advisor will be completed before or in parallel with this hardening.
- Workspace context retrieval will be fast enough to not add perceptible latency.
- The learning store currently contains enough similar intents to serve as few-shot templates.
- Streaming integration will fit into the existing TUI without major refactors.
- Eval framework can be extended to measure per-command success with minimal effort.
- A fast, cheap clarification model (e.g., gpt-4o-mini) will be available and can be routed by the advisor.
- Recommendations:
- Prioritize subtasks 2 (retry ladder) and 1 (structured output gating), as they directly address the dominant failure modes.
- Reuse the existing orchestrator retry pattern, adapted for the simpler create flow (e.g., 2-3 retries).
- Leverage existing TUI progress components from the agent layer for streaming.
- Set up create eval as early as possible to establish a baseline and validate improvements.
- Coordinate with the Model Capability Advisor note to ensure a testable integration point.

## Triage result
- Date: 2026-06-19T20:35:15.270Z
- Classification: execution work
- Routing: Created GitHub issue #752 and routed it to Needs Human Acceptance.
- GitHub artifacts:
  - https://github.com/hgahub/duumbi/issues/752
- Obsidian artifacts:
  - none
- Canonical duplicate:
  - none
- Open questions:
  - See GitHub issue.
- Assumptions:
  - Automated triage refill selected this source as actionable. Rationale: Current human acceptance count is 2, target minimum is 3. No eligible Todo issue represents this M1 critical-priority work. Intent Create Hardening is the most recently enriched inbox note, classified as critical value and high importance, and represents actionable next work for the project.
- Next stage: Needs Human Acceptance
