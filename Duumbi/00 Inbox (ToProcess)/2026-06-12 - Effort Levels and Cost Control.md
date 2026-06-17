---
tags:
  - duumbi/inbox/enriched
  - duumbi/status/processed
  - duumbi/classification/feature
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/medium
duumbi_inbox_enrichment: processed
duumbi_inbox_enrichment_generated_at: 2026-06-17T18:50:04.101Z
---

# Effort Levels and Cost Control

<!-- duumbi-inbox-enrichment:v1 status=processed generated_at=2026-06-17T18:50:04.101Z -->

## Source
- Surface: Manual Obsidian edit
- Vault path: Duumbi/00 Inbox (ToProcess)/2026-06-12 - Effort Levels and Cost Control.md
- Submitted by: unknown unless explicit in the raw input

## Raw input
> ---
> tags:
>   - duumbi/inbox/roadmap
>   - duumbi/status/to-process
>   - duumbi/classification/execution
>   - duumbi/value/high
>   - duumbi/importance/high
>   - duumbi/complexity/medium
> created: 2026-06-12
> milestone: M1
> source: "[[DUUMBI Future Development Roadmap Map]]"
> ---
> 
> # Effort Levels and Cost Control
> 
> ## Context
> 
> Today the user has no effort/cost lever (verified 2026-06-12): complexity is auto-derived from a crude proxy (test-case count → Simple/Moderate/Complex in `src/agents/analyzer.rs`), the agent team comes from a fixed 9-row lookup table, and `AgentPolicy` has token budgets (`budget_per_intent`, `budget_per_session`, alert threshold) but no user-facing control surface. Cost per call is already recorded (`cost_usd` in `ModelCallEvent`) yet never previewed or summarized for the user. Requested capability: specify an effort level for work, directly influencing cost.
> 
> ## Goal
> 
> A user-selectable effort level (low / medium / high / max) at session, intent, and command level that maps to model tier, retry counts, context budget, team size, and verification depth — with a cost estimate before execution and a cost report after.
> 
> ## Subtasks
> 
> 1. Effort plumbing: `--effort` flag on CLI commands (`intent create/execute`, `add`), `/effort` in the TUI, optional `effort:` field in the intent spec YAML, session default in config; precedence: command > intent > session.
> 2. Mapping table (data, not code): effort → model tier (via [[2026-06-12 - Model Capability Advisor and Task Routing]]), mutation/repair retry counts, context token budget, agent team modifier (e.g. high adds Reviewer even on Moderate tasks), verification depth (tests only → +property tests → +verify when available), clarification pass on/off.
> 3. User complexity override: allow overriding the derived Simple/Moderate/Complex profile — the test-case-count proxy is often wrong; effort and complexity are distinct inputs to team assembly.
> 4. Cost preview: before execute, estimate a cost range from catalog pricing × historical per-task token averages (model-performance EWMA) — "estimated $0.12–0.35 at effort=medium; proceed?"; respect the configured alert threshold.
> 5. Hard budget enforcement: `budget_per_intent` becomes an enforced stop with graceful abort (state saved, resumable), not just an alert; TUI shows budget consumption live during execute.
> 6. Cost reporting: per-intent cost in `intent status/review` output and archived ExecutionMeta; per-session rollup in session stats; both feed the [[2026-06-12 - Token Economics Benchmark]] release metrics.
> 
> ## Acceptance criteria
> 
> - The same intent run at `--effort low` vs `--effort high` shows measurably different cost, retries, and verification depth, both visible in the report.
> - Cost preview is within ±50% of actual on the eval corpus.
> - Hitting the intent budget mid-execute stops cleanly and the intent is resumable.
> 
> ## Links
> 
> - [[DUUMBI Future Development Roadmap Map]]
> - [[2026-06-12 - Model Capability Advisor and Task Routing]]
> - [[2026-06-12 - Token Economics Benchmark]]

## Interpreted intent

Provide a user-selectable effort level (low/medium/high/max) at session, intent, and command level that maps to model tier, retry counts, context budget, team size, and verification depth, with a cost estimate before execution and a cost report after.

## Developer summary

DUUMBI currently has no user-facing effort lever: complexity is auto-derived from a test-case count heuristic, the agent team comes from a fixed lookup table, and AgentPolicy has token budgets but no user control or enforcement. Cost per call is recorded (ModelCallEvent.cost_usd) but never previewed or summarized.

This feature adds a user-selectable effort level (low/medium/high/max) at session, intent, and command level, with precedence: command > intent > session. An effort mapping table (data, not code) translates effort into model tier (via Model Capability Advisor), mutation/repair retry counts, context token budget, agent team modifier, verification depth, and clarification pass toggle. A user complexity override decouples the auto-derived Simple/Moderate/Complex profile from the effort decision.

Before execution, a cost preview estimates a range from catalog pricing and historical per-task token averages (EWMA). A configured alert threshold warns the user. During execution, budget consumption is shown live, and the budget_per_intent is enforced with a graceful abort that saves state and allows resumption. After execution, per-intent and per-session cost reports are produced, feeding into the Token Economics Benchmark metrics.

## UML overview

```mermaid
sequenceDiagram
    actor User
    participant CLI/TUI
    participant EffortResolver
    participant EffortMappingTable
    participant CostEstimator
    participant Orchestrator
    participant BudgetEnforcement
    participant Reporting

    User->>CLI/TUI: Set effort (--effort high, /effort, or config)
    CLI/TUI->>EffortResolver: resolve(effort, session_default, intent_spec)
    EffortResolver->>EffortMappingTable: map(effort) → model tier, retries, context, team, verification
    EffortMappingTable-->>EffortResolver: MappedSettings
    EffortResolver-->>CLI/TUI: resolved settings

    opt Before execute
        CLI/TUI->>CostEstimator: estimate(task, MappedSettings)
        CostEstimator->>CostEstimator: catalog price × EWMA token avg
        CostEstimator-->>CLI/TUI: cost range + alert threshold check
        CLI/TUI-->>User: "Estimated $0.12–0.35 at effort=medium; proceed?"
        User->>CLI/TUI: confirm
    end

    CLI/TUI->>Orchestrator: execute(intent, MappedSettings)
    loop each call
        Orchestrator->>BudgetEnforcement: check budget
        alt budget exceeded
            BudgetEnforcement-->>Orchestrator: stop, save state
            Orchestrator-->>CLI/TUI: graceful abort, state saved, resumable
        else within budget
            BudgetEnforcement-->>Orchestrator: allow
            Orchestrator->>LLM: call using model tier, retries
            LLM-->>Orchestrator: result + token usage
            Orchestrator->>BudgetEnforcement: record consumption
            Orchestrator->>CLI/TUI: live budget display
        end
    end

    Orchestrator->>Reporting: per-intent cost, per-session rollup
    Reporting-->>CLI/TUI: cost report
    CLI/TUI-->>User: view in intent status/review, session stats
```

## Classification
- Type: feature
- Business value: high
- Importance: high
- Complexity: medium

## Clarifications
### Answered
- The user currently has no effort/cost lever; complexity is auto-derived from test-case count in src/agents/analyzer.rs.
- Agent team is assembled from a fixed 9-row lookup table.
- AgentPolicy has budget_per_intent, budget_per_session, and an alert threshold, but no user-facing control surface.
- Cost per call is recorded in ModelCallEvent.cost_usd but never previewed or summarized for the user.
- The Model Capability Advisor (from another inbox item) will be used for model tier mapping.

### Open
- Where exactly should the effort mapping table live? (TOML config file, built-in data structure, or a separate configuration artifact?)
- How should the user complexity override interact with the effort level? (Should complexity override be independent or influenced by effort?)
- What is the exact calibration strategy for the cost estimate? (How are per-task token averages maintained and how is the EWMA computed?)
- Should the mapping table include specific model names, or only tiers that delegate to the Model Capability Advisor?
- What UI surfaces beyond CLI and TUI need to support effort selection? (e.g., Studio, MCP, future integrations)

## Relevant DUUMBI context
- src/agents/analyzer.rs - complexity heuristic derivation from test-case count
- src/agents/orchestrator.rs - AgentPolicy usage with budget fields
- src/agents/model_performance.rs - token usage and cost tracking
- src/agents/model_catalog.rs - model pricing catalog
- Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Future Development Roadmap Map.md - overall roadmap
- Duumbi/00 Inbox (ToProcess)/2026-06-12 - Model Capability Advisor and Task Routing.md - linked model routing
- Duumbi/00 Inbox (ToProcess)/2026-06-12 - Token Economics Benchmark.md - linked benchmark metrics

## Related GitHub context

No existing GitHub issue or PR identified for this feature. Triage should verify later if any related issues exist (e.g., for cost preview or budget enforcement).

## Initial routing recommendation

GitHub issue

## Requested follow-up
- Create a GitHub issue from this enrichment with clear acceptance criteria and linked dependencies.
- Draft a technical spec covering integration points, mapping table format, and cost estimation algorithm.

## AI agent instructions
- When creating the GitHub issue, link to this inbox note and the Model Capability Advisor and Token Economics dependencies.
- Break the issue into subtasks matching the note's subtasks (plumbing, mapping table, complexity override, cost preview, budget enforcement, cost reporting).
- Specify that the mapping table must be data-driven (e.g., TOML) and not hard-coded logic.
- Ensure the cost preview uses existing model_performance EWMA and catalog pricing; alert threshold is configurable.
- The budget enforcement must be hard: abort gracefully, save state, mark as resumable, and show live consumption in TUI.
- Request a spec review before implementation.

## Scope candidate
### In
- Effort level selection via --effort flag, /effort in TUI, optional `effort:` field in intent spec YAML, and session default in config.
- Resolution precedence: command > intent > session.
- Effort mapping table (data) translating effort to model tier, retries, context budget, agent team modifier, verification depth, clarification pass.
- User complexity override to correct auto-derived Simple/Moderate/Complex profile.
- Cost preview before execute using catalog pricing and per-task token averages (EWMA).
- Hard budget enforcement with graceful abort and resumable state.
- Live budget consumption display in TUI during execute.
- Per-intent cost in `intent status/review` and archived ExecutionMeta; per-session rollup; feed into Token Economics Benchmark.

### Out
- Changes to the actual model routing algorithm (handled by Model Capability Advisor).
- Dynamic adjustment of agent team beyond the mapping table (future extension).
- Cloud or remote cost attribution beyond local sessions.
- Changing the existing AgentPolicy structure beyond what's needed for effort integration.

## Risks and trade-offs
- Cost estimate accuracy depends on representative per-task token averages; may drift if average tasks change in complexity.
- Mapping table could become a maintenance burden if model tiers change frequently.
- A high-effort setting combined with a complex intent could still lead to high costs, requiring clear user awareness.
- Complexity override might lead users to mis-categorize intent complexity, affecting team assembly.

## Obsidian tags

#duumbi/inbox/enriched #duumbi/status/processed #duumbi/classification/feature #duumbi/value/high #duumbi/importance/high #duumbi/complexity/medium

## Enrichment result
- Date: 2026-06-17T18:50:04.101Z
- Status: ready for triage
- Canonical duplicate: none verified
- Facts:
- No effort lever exists today; complexity is derived from test-case count.
- Agent team is selected from a fixed 9-row table.
- Token budgets exist in AgentPolicy but are not enforced or user-controllable.
- Cost per call is recorded (ModelCallEvent.cost_usd) but not previewed or reported to the user.
- The note is tagged as milestone M1 and is linked from the roadmap.
- Assumptions:
- The Model Capability Advisor and Task Routing feature (separate inbox item) will be implemented and available for model tier mapping.
- The Token Economics Benchmark will exist to consume cost reports.
- Cost preview can be calibrated to within ±50% of actual on the eval corpus.
- The user wants effort levels rather than direct cost caps.
- Recommendations:
- Implement effort plumbing and mapping table first, then cost preview, then budget enforcement, then reporting.
- Use a TOML file for the mapping table, versioned and updatable independently.
- Integrate cost preview with the existing model_performance EWMA and model_catalog pricing.
- Enforce budget_per_intent with a hard stop and state persistence (resumable intent).
- Report per-intent cost in archived ExecutionMeta and surface it in `intent status/review`.
- Ensure the TUI live budget display uses existing tracing/event infrastructure.
