# 2026-05-14 - IntentSpec Preflight Quality Gate

## Source
- Source: Codex
- Surface: Codex
- Conversation context: Local Codex conversation inspected the DUUMBI `intent create` and `intent execute` flow, produced an infographic, then identified the highest-priority implementation task.
- Submitted by: User

## Raw input
The user asked what the most important implementation task is based on the collected DUUMBI intent-pipeline information, then asked Codex to capture that task in the DUUMBI knowledge-base Inbox using the `duumbi-codex-intake` skill.

## Interpreted intent
Implement a deterministic `IntentSpec` preflight / quality gate between `intent create` and `intent execute`. The gate should validate, score, and enrich an intent spec before graph mutation begins, so weak or incomplete specs do not cascade into bad task decomposition, poor LLM graph patches, retries, repair loops, and unreliable verification.

The intended implementation shape is a new module such as `src/intent/preflight.rs`, called after `intent create` and at the start of `intent execute`. It should produce a structured report with severity, score, issues, suggested fixes, reuse candidates, and decomposition hints.

## Classification
feature proposal; architecture decision; execution task

## Clarifications
### Answered
- The user explicitly asked to capture this task in the Inbox using the Stage 2 Codex intake skill.
- The intended priority was stated in the prior Codex answer: this is the most important implementation task because it controls the upstream quality of the rest of the pipeline.

### Open
- Should the preflight gate block execution on severe issues, or only warn in the first version?
- Should automatic spec repair be in scope for v1, or should v1 only report issues and suggested fixes?
- What exact scoring thresholds should map to pass, warn, and block?
- Should repository reuse candidates come only from workspace graphs in v1, or also from vendor/cache repositories?

## Relevant DUUMBI context
- `Duumbi/How to use.md`: Obsidian Inbox stores raw material before triage; GitHub is the execution source of truth; specs should make agent behavior unambiguous before implementation.
- `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - PRD.md`: DUUMBI is intent-first, graph-centered, evidence-oriented, and should reduce drift between intent, implementation, execution state, and durable knowledge.
- `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - Glossary.md`: Intent should be clarified into explicit defaults, inputs, outputs, edge cases, invariants, and verification steps before implementation.
- `Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Agentic Development Map.md`: Read-only context and risk inspection should happen before mutation; execution work should be routed through GitHub after triage.
- `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - Development Intake to Delivery Workflow.md`: Stage 2 Codex intake creates one raw Inbox capture for later Stage 4 triage.
- Local source inspected earlier in this Codex thread: `src/intent/create.rs`, `src/intent/execute.rs`, `src/intent/coordinator.rs`, `src/context/mod.rs`, `src/context/analyzer.rs`, `src/graph/builder.rs`, `src/graph/validator.rs`, `src/intent/verifier.rs`.

## Related GitHub context
Not inspected; triage should verify GitHub state later.

## Initial routing recommendation
GitHub issue, with possible follow-up Atlas Dot or architecture note after triage.

## Requested follow-up
- Capture the task in `Duumbi/00 Inbox (ToProcess)/`.
- Later triage should decide whether this becomes an accepted GitHub issue, a product/architecture note, or both.
- If accepted for execution, define a bounded v1 technical spec for an `IntentSpec` preflight / quality gate.

## Notes
- Facts:
  - `intent create` currently uses LLM-assisted spec generation and has known benchmark fallback behavior when parsing fails.
  - `intent execute` already has downstream protections: deterministic task decomposition, context assembly, LLM graph mutation, graph validation, verifier tests, repair, and learning logs.
  - The coordinator currently uses a rule-based deterministic decomposition strategy.
  - Context assembly already scans workspace/vendor/cache graph modules and can inject module summaries, graph fragments, few-shot successes, and recent failures.
- Assumptions:
  - Upstream `IntentSpec` quality is a major driver of downstream instability, retries, and repair cost.
  - A deterministic preflight report can improve reliability before introducing more complex LLM-based planning.
  - The first implementation should be conservative and inspectable rather than another opaque LLM step.
- Recommendations:
  - Start with deterministic validation and scoring before any auto-repair behavior.
  - Include checks for acceptance criteria/test-case alignment, module naming, `app/main` presence, duplicate or missing targets, DUUMBI type constraints, edge-case coverage, and repository reuse candidates.
  - Produce a structured report type such as `IntentPreflightReport { severity, score, issues, suggested_fixes, reuse_candidates, decomposition_hints }`.
  - Integrate the report into both `intent create` and `intent execute`, so users can catch poor specs early and execution can refuse clearly invalid inputs when needed.

## Triage result
- Date: 2026-05-14
- Classification: execution work
- Routing: Created GitHub Issue #553 in `hgahub/duumbi`, added it to the DUUMBI Project, and set Project Status to `Needs Human Acceptance`.
- GitHub artifacts:
  - https://github.com/hgahub/duumbi/issues/553
- Obsidian artifacts: none; no durable Atlas artifact was created because the stable reusable knowledge should be captured after human acceptance/specification or implementation evidence, not during execution triage.
- Canonical duplicate: none found. Stage 4 inspected related closed issues/PRs around `IntentSpec`, `intent create`, `intent execute`, context assembly, and Phase 15 intent reliability; no duplicate preflight-gate issue or Discussion was found.
- Open questions:
  - Should severe preflight findings block `intent execute` in v1, or should v1 warn and require explicit confirmation?
  - Should automatic spec repair be out of scope for v1, or should the gate produce machine-applicable fixes from the start?
  - What score thresholds should map to pass, warn, and block?
  - Should reuse candidates come only from workspace graph modules in v1, or also from vendor/cache repositories?
  - Should preflight results be persisted into the intent YAML execution metadata, or only shown during create/execute?
- Assumptions:
  - The first version should remain deterministic and inspectable rather than adding another opaque LLM step.
  - The issue is primarily execution work; architecture knowledge should be promoted to the Atlas only after the accepted scope or implementation behavior stabilizes.
- Next stage: Stage 5 Human Acceptance

## Closure disposition
- Date: 2026-05-15
- GitHub issue: https://github.com/hgahub/duumbi/issues/553
- Merged implementation PR: https://github.com/hgahub/duumbi/pull/558
- Product spec: `specs/DUUMBI-553/PRODUCT.md`, merged via https://github.com/hgahub/duumbi/pull/554
- Technical spec: `specs/DUUMBI-553/TECHNICAL.md`, merged via https://github.com/hgahub/duumbi/pull/557
- Stage 11 review artifact: https://github.com/hgahub/duumbi/pull/558#issuecomment-4455326324
- Stage 12 closure evidence: https://github.com/hgahub/duumbi/issues/553#issuecomment-4462464747
- Outcome: Completed. Issue #553 is closed as completed and its DUUMBI Project status is `Done`.
- Durable knowledge sync: Not needed during closure; the reusable behavior is captured in the approved specs and merged source implementation.
