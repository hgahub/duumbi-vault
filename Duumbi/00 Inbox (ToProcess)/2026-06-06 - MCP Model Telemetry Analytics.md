# 2026-06-06 - MCP Model Telemetry Analytics

## Source
- Source: Codex
- Surface: Codex
- Conversation context: While refining GitHub issue `hgahub/duumbi#675`, the user asked to inspect how DUUMBI learns from model usage and what statistics it records, then capture a follow-up idea for MCP-based analysis.
- Submitted by: Gabor Heizer

## Raw input
The user asked, in Hungarian, to review how DUUMBI learns during model usage and what statistics it records. They suggested that it would be useful to query this through MCP so regular statistics and analyses could be produced, and asked to record this as a new idea through the DUUMBI Codex intake process.

## Interpreted intent
Create a product/architecture follow-up for exposing DUUMBI's existing local model-usage knowledge through MCP. DUUMBI already records model-access probe metadata and model-performance telemetry. A new MCP-facing surface could let developers or analysis agents ask structured questions such as which models are accessible for a credential, which models fail most often, which provider/model combinations are slow or costly, and where telemetry is stale.

The goal is read-only analytics and decision support, not automatic routing changes, cloud telemetry ingestion, or exposing secrets.

## Classification
feature proposal; architecture decision; execution task

## Clarifications
### Answered
- The idea is separate from the provider catalog refresh issue.
- The desired interface is MCP-queryable, so analysis can be repeated regularly.
- Existing DUUMBI telemetry should be inspected and reused rather than inventing a new data store first.

### Open
- Should this be implemented as new MCP resources, MCP tools, CLI query commands, or a shared analytics layer used by all of them?
- Should analytics be local-only in V1, or should there later be an optional export/report artifact?
- What privacy boundary is required for credential fingerprints, provider error snippets, task profiles, and cost/latency data?
- Which first questions matter most: accessibility by provider, success/failure rates by model, latency/cost trends, routing effectiveness, stale probes, or provider setup health?
- Should the analytics include only aggregate data by default, with raw event inspection behind an explicit flag?

## Relevant DUUMBI context
- `Duumbi/How to use.md`: Obsidian Inbox is for raw captures and unresolved research input; GitHub remains execution source of truth.
- `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - PRD.md`: DUUMBI should answer read-only questions over source, graph, intents, sessions, knowledge, and evidence before mutation begins.
- `Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Agentic Development Map.md`: ask read-only questions first and route execution work to GitHub.
- `Duumbi/01 Atlas (Knowledge Base)/Dots (Atomic Ideas)/MCP Resources and Prompts.md`: MCP resources expose structured context and MCP prompts expose repeatable workflows.
- `hgahub/duumbi/src/agents/model_access.rs`: persists provider/model accessibility under `~/.duumbi/knowledge/model-access`, including `current.json` and append-only `events.jsonl`, keyed by a non-reversible credential fingerprint.
- `hgahub/duumbi/src/agents/model_performance.rs`: persists model-selection telemetry under workspace `.duumbi/knowledge/model-performance`, including append-only `events.jsonl` and rolling `aggregates.json`.
- `ModelCallEvent` records provider, model, agent role, template version, task type, complexity, scope, risk, token counts, reasoning tokens, latency, first-token latency, estimated cost, parse success, patch count, validation errors, retry count, and final outcome.

## Related GitHub context
- `https://github.com/hgahub/duumbi/issues/675`: adjacent open provider catalog refresh issue; updated to mention this MCP telemetry analytics idea as a separate follow-up.
- `https://github.com/hgahub/duumbi/issues/610`: related closed CI/workflow LLM usage metrics work; adjacent but not a duplicate because this idea targets DUUMBI's local model-access/model-performance knowledge and MCP queryability.
- GitHub search did not find an exact duplicate for MCP-based model telemetry analytics.

## Initial routing recommendation
GitHub issue

Stage 4 triage should create or select a scoped GitHub issue for a read-only MCP analytics surface over local model telemetry. This should likely be separate from #675 because #675 is catalog publication/update behavior, while this idea is about querying observed DUUMBI model usage and accessibility.

## Requested follow-up
- Record this idea for later triage.
- Consider it as a separate task from provider catalog refresh.
- Use the existing telemetry stores as the starting evidence.
- Do not implement it from this intake note alone.

## Notes
- Facts:
  - DUUMBI currently records model-access probe results in user-level knowledge storage.
  - DUUMBI currently records model-performance events and aggregates in workspace-level knowledge storage.
  - Existing fields are already sufficient for useful provider/model analysis if exposed safely.
  - #610 covered GitHub Actions/workflow usage metrics and is closed; it does not replace this local DUUMBI telemetry MCP idea.
- Assumptions:
  - V1 should be local and read-only.
  - MCP resources or tools should avoid exposing raw secrets, raw prompts, model completions, or unnecessarily detailed provider error bodies.
  - Aggregated views should be the default, with raw events only if explicitly authorized.
- Recommendations:
  - Start with MCP resources/tools for aggregate questions: accessible models by provider, denied/auth-failed/unknown counts, success/failure by provider/model/task profile, EWMA latency/cost, stale access probes, and recent validation/parse failure trends.
  - Define a privacy contract before exposing raw event logs.
  - Keep routing changes out of V1; use the analytics as evidence for human/provider-catalog decisions first.
