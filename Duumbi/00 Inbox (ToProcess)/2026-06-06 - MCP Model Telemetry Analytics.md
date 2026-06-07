---
tags:
  - duumbi/inbox/enriched
  - duumbi/status/processed
  - duumbi/classification/feature
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/medium
duumbi_inbox_enrichment: processed
duumbi_inbox_enrichment_generated_at: 2026-06-07T18:27:49.808Z
---

# MCP Model Telemetry Analytics

<!-- duumbi-inbox-enrichment:v1 status=processed generated_at=2026-06-07T18:27:49.808Z -->

## Source
- Surface: Manual Obsidian edit
- Vault path: Duumbi/00 Inbox (ToProcess)/2026-06-06 - MCP Model Telemetry Analytics.md
- Submitted by: unknown unless explicit in the raw input

## Raw input
> # 2026-06-06 - MCP Model Telemetry Analytics
> 
> ## Source
> - Source: Codex
> - Surface: Codex
> - Conversation context: While refining GitHub issue `hgahub/duumbi#675`, the user asked to inspect how DUUMBI learns from model usage and what statistics it records, then capture a follow-up idea for MCP-based analysis.
> - Submitted by: Gabor Heizer
> 
> ## Raw input
> The user asked, in Hungarian, to review how DUUMBI learns during model usage and what statistics it records. They suggested that it would be useful to query this through MCP so regular statistics and analyses could be produced, and asked to record this as a new idea through the DUUMBI Codex intake process.
> 
> ## Interpreted intent
> Create a product/architecture follow-up for exposing DUUMBI's existing local model-usage knowledge through MCP. DUUMBI already records model-access probe metadata and model-performance telemetry. A new MCP-facing surface could let developers or analysis agents ask structured questions such as which models are accessible for a credential, which models fail most often, which provider/model combinations are slow or costly, and where telemetry is stale.
> 
> The goal is read-only analytics and decision support, not automatic routing changes, cloud telemetry ingestion, or exposing secrets.
> 
> ## Classification
> feature proposal; architecture decision; execution task
> 
> ## Clarifications
> ### Answered
> - The idea is separate from the provider catalog refresh issue.
> - The desired interface is MCP-queryable, so analysis can be repeated regularly.
> - Existing DUUMBI telemetry should be inspected and reused rather than inventing a new data store first.
> 
> ### Open
> - Should this be implemented as new MCP resources, MCP tools, CLI query commands, or a shared analytics layer used by all of them?
> - Should analytics be local-only in V1, or should there later be an optional export/report artifact?
> - What privacy boundary is required for credential fingerprints, provider error snippets, task profiles, and cost/latency data?
> - Which first questions matter most: accessibility by provider, success/failure rates by model, latency/cost trends, routing effectiveness, stale probes, or provider setup health?
> - Should the analytics include only aggregate data by default, with raw event inspection behind an explicit flag?
> 
> ## Relevant DUUMBI context
> - `Duumbi/How to use.md`: Obsidian Inbox is for raw captures and unresolved research input; GitHub remains execution source of truth.
> - `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - PRD.md`: DUUMBI should answer read-only questions over source, graph, intents, sessions, knowledge, and evidence before mutation begins.
> - `Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Agentic Development Map.md`: ask read-only questions first and route execution work to GitHub.
> - `Duumbi/01 Atlas (Knowledge Base)/Dots (Atomic Ideas)/MCP Resources and Prompts.md`: MCP resources expose structured context and MCP prompts expose repeatable workflows.
> - `hgahub/duumbi/src/agents/model_access.rs`: persists provider/model accessibility under `~/.duumbi/knowledge/model-access`, including `current.json` and append-only `events.jsonl`, keyed by a non-reversible credential fingerprint.
> - `hgahub/duumbi/src/agents/model_performance.rs`: persists model-selection telemetry under workspace `.duumbi/knowledge/model-performance`, including append-only `events.jsonl` and rolling `aggregates.json`.
> - `ModelCallEvent` records provider, model, agent role, template version, task type, complexity, scope, risk, token counts, reasoning tokens, latency, first-token latency, estimated cost, parse success, patch count, validation errors, retry count, and final outcome.
> 
> ## Related GitHub context
> - `https://github.com/hgahub/duumbi/issues/675`: adjacent open provider catalog refresh issue; updated to mention this MCP telemetry analytics idea as a separate follow-up.
> - `https://github.com/hgahub/duumbi/issues/610`: related closed CI/workflow LLM usage metrics work; adjacent but not a duplicate because this idea targets DUUMBI's local model-access/model-performance knowledge and MCP queryability.
> - GitHub search did not find an exact duplicate for MCP-based model telemetry analytics.
> 
> ## Initial routing recommendation
> GitHub issue
> 
> Stage 4 triage should create or select a scoped GitHub issue for a read-only MCP analytics surface over local model telemetry. This should likely be separate from #675 because #675 is catalog publication/update behavior, while this idea is about querying observed DUUMBI model usage and accessibility.
> 
> ## Requested follow-up
> - Record this idea for later triage.
> - Consider it as a separate task from provider catalog refresh.
> - Use the existing telemetry stores as the starting evidence.
> - Do not implement it from this intake note alone.
> 
> ## Notes
> - Facts:
>   - DUUMBI currently records model-access probe results in user-level knowledge storage.
>   - DUUMBI currently records model-performance events and aggregates in workspace-level knowledge storage.
>   - Existing fields are already sufficient for useful provider/model analysis if exposed safely.
>   - #610 covered GitHub Actions/workflow usage metrics and is closed; it does not replace this local DUUMBI telemetry MCP idea.
> - Assumptions:
>   - V1 should be local and read-only.
>   - MCP resources or tools should avoid exposing raw secrets, raw prompts, model completions, or unnecessarily detailed provider error bodies.
>   - Aggregated views should be the default, with raw events only if explicitly authorized.
> - Recommendations:
>   - Start with MCP resources/tools for aggregate questions: accessible models by provider, denied/auth-failed/unknown counts, success/failure by provider/model/task profile, EWMA latency/cost, stale access probes, and recent validation/parse failure trends.
>   - Define a privacy contract before exposing raw event logs.
>   - Keep routing changes out of V1; use the analytics as evidence for human/provider-catalog decisions first.

## Interpreted intent

Expose DUUMBI's existing local model-usage telemetry (model-access probes and model-performance events/aggregates) through read-only MCP resources or tools so developers or analysis agents can answer structured questions like: which models are accessible for a credential, which models fail most often, which provider/model combinations are slow or costly, and where telemetry is stale. This is a product/architecture follow-up for decision support, not automatic routing changes.

## Developer summary

Implement one or more MCP resources and/or tools that read from the existing `~/.duumbi/knowledge/model-access/` and `.duumbi/knowledge/model-performance/` stores to provide aggregate read-only analytics over model usage. V1 should be local-only and keep routing changes out. A privacy contract must prevent leaking credential fingerprints, raw prompts, completions, or provider error bodies; default to aggregated views, with raw event access behind an explicit flag. The MCP surface should answer: accessible models per provider, denial/auth-failure counts, success/failure rates by provider/model/task profile, EWMA latency/cost, stale access probes, and recent parse/validation failure trends. Reuse existing telemetry data without creating a new store. Design the interface so later stages can add reporting artifacts, export, or deeper querying.

## UML overview

```mermaid
classDiagram
    class MCPServer {
        +list_tools()
        +call_tool(name, args)
    }
    class ModelAccessAnalyzer {
        +accessible_models_by_provider()
        +access_denials_by_provider()
        +stale_probes()
    }
    class ModelPerformanceAnalyzer {
        +success_failure_by_model()
        +latency_cost_ewma()
        +parse_validation_errors()
    }
    MCPServer --> ModelAccessAnalyzer : uses
    MCPServer --> ModelPerformanceAnalyzer : uses
    ModelAccessAnalyzer --> "~/.duumbi/knowledge/model-access" : reads
    ModelPerformanceAnalyzer --> ".duumbi/knowledge/model-performance" : reads
```

## Classification
- Type: feature
- Business value: high
- Importance: high
- Complexity: medium

## Clarifications
### Answered
- Separate from provider catalog refresh (issue #675).
- MCP-queryable interface desired.
- Existing telemetry stores (model-access, model-performance) should be reused.

### Open
- Should this be implemented as new MCP resources, MCP tools, CLI query commands, or a shared analytics layer used by all of them?
- Should V1 be local-only, or should there later be an optional export/report artifact?
- What privacy boundary is required for credential fingerprints, provider error snippets, task profiles, and cost/latency data?
- Which first questions matter most: accessibility by provider, success/failure rates by model, latency/cost trends, routing effectiveness, stale probes, or provider setup health?
- Should analytics include only aggregate data by default, with raw event inspection behind an explicit flag?

## Relevant DUUMBI context
- Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - PRD.md: DUUMBI should answer read-only questions over source, graph, intents, sessions, knowledge, and evidence before mutation begins.
- Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Agentic Development Map.md: Ask read-only questions first and route execution work to GitHub.
- Duumbi/01 Atlas (Knowledge Base)/Dots (Atomic Ideas)/MCP Resources and Prompts.md: MCP resources expose structured context and MCP prompts expose repeatable workflows.
- hgahub/duumbi/src/agents/model_access.rs: persists provider/model accessibility under ~/.duumbi/knowledge/model-access, including current.json and events.jsonl, keyed by a non-reversible credential fingerprint.
- hgahub/duumbi/src/agents/model_performance.rs: persists model-selection telemetry under workspace .duumbi/knowledge/model-performance, including events.jsonl and aggregates.json.

## Related GitHub context

https://github.com/hgahub/duumbi/issues/675 (adjacent open provider catalog refresh issue) and https://github.com/hgahub/duumbi/issues/610 (closed CI/workflow LLM usage metrics, not a duplicate). Triage should verify for later duplicate detection.

## Initial routing recommendation

GitHub issue

## Requested follow-up
- Record this idea for later triage.
- Consider it as a separate task from provider catalog refresh.
- Use the existing telemetry stores as the starting evidence.
- Do not implement it from this intake note alone.

## AI agent instructions
- When creating the GitHub issue, include a concise problem statement: DUUMBI lacks a read-only MCP query surface for the model-usage telemetry it already collects.
- List the existing data sources: ~/.duumbi/knowledge/model-access and .duumbi/knowledge/model-performance.
- Specify that the first implementation (V1) must be local and read-only, with no automatic routing changes.
- Define the exact MCP resources or tools to expose, using the open clarifications as a guide; propose starting with aggregate analytics (accessible models, denial counts, success/failure rates, EWMA latency/cost, stale probes, parse/validation error trends).
- Describe the privacy contract: no raw credential fingerprints, provider error snippets, prompts, completions, or cost/latency breakdowns by default; raw events only with an explicit authorization flag.
- Advise that implementation should be in src/mcp/tools.rs or a new analytics module, reusing the existing model_access and model_performance data readers.
- Remind that routing changes (e.g., automatic provider/model selection adjustments) are explicitly out of scope for V1.
- Add a note that this issue is separate from #675 (provider catalog refresh) and #610 (GitHub Actions metrics), and link them as related.

## Scope candidate
### In
- MCP resources and/or tools that answer aggregate questions about model accessibility and performance.
- Read-only local access to existing model-access and model-performance telemetry.
- Default aggregated views with no raw event exposure unless authorized.
- Privacy contract to protect credential fingerprints, error bodies, and fine-grained task data.
- Documentation for the new MCP interface in DUUMBI’s developer docs.

### Out
- Automatic routing or provider selection changes.
- Cloud telemetry ingestion or centralized analytics service.
- Raw prompt or completion storage inspection.
- Exposing provider secret keys or full credential details.
- Real-time alerting or notifications.
- Implementation of a new data store; existing telemetry should be reused.

## Risks and trade-offs
- Accidental exposure of credential fingerprints or provider error details if privacy boundaries not strictly enforced.
- Performance degradation if large event logs are scanned without pagination or caching.
- Misinterpretation of stale telemetry data leading to incorrect decisions.
- Scope creep into automatic routing changes that could destabilize model selection without human approval.

## Obsidian tags

#duumbi/inbox/enriched #duumbi/status/processed #duumbi/classification/feature #duumbi/value/high #duumbi/importance/high #duumbi/complexity/medium

## Enrichment result
- Date: 2026-06-07T18:27:49.808Z
- Status: ready for triage
- Canonical duplicate: none verified
- Facts:
- DUUMBI currently records model-access probe results in user-level knowledge storage (~/.duumbi/knowledge/model-access).
- DUUMBI currently records model-performance events and aggregates in workspace-level knowledge storage (.duumbi/knowledge/model-performance).
- Existing telemetry fields (provider, model, success, latency, cost, task profile, etc.) are already sufficient for useful provider/model analysis.
- Issue #610 covered GitHub Actions/workflow usage metrics and is closed; it does not replace this local DUUMBI telemetry MCP idea.
- The idea was captured as a follow-up to issue #675 (provider catalog refresh) and is intended as a separate work item.
- Assumptions:
- V1 implementation will be local and read-only.
- MCP resources or tools will avoid exposing raw secrets, raw prompts, model completions, or unnecessarily detailed provider error bodies.
- Aggregated views will be the default, with raw events only if explicitly authorized.
- The feature will be implemented incrementally, starting with the most immediately useful analytics questions.
- Duplicate detection at triage will confirm that no similar MCP telemetry issue already exists.
- Recommendations:
- Start with MCP resources/tools for aggregate questions: accessible models by provider, denied/auth-failed/unknown counts, success/failure by provider/model/task profile, EWMA latency/cost, stale access probes, and recent validation/parse failure trends.
- Define a privacy contract before exposing raw event logs.
- Keep routing changes out of V1; use the analytics as evidence for human/provider-catalog decisions first.
- Route to a dedicated GitHub issue separate from #675 to avoid scope mixing.
