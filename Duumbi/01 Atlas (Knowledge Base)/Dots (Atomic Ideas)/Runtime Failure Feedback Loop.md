---
tags:
  - project/duumbi
  - dot/product-capability
  - capability/runtime-feedback
status: active
created: 2026-05-21
updated: 2026-05-21
related_maps:
  - "[[DUUMBI Core Concepts Map]]"
related_works:
  - "[[DUUMBI - PRD]]"
  - "[[DUUMBI - Service and Research Direction]]"
---
# Runtime Failure Feedback Loop

DUUMBI's planned runtime failure feedback loop helps a developer diagnose and repair failures that happen while developing, testing, or running a DUUMBI-built application in a controlled developer environment.

The first product promise is not production auto-repair for end-user installations. The first promise is local, evidence-backed developer feedback:

1. The developer builds or runs with tracing enabled.
2. The compiled application fails at runtime.
3. DUUMBI records trace and crash evidence locally.
4. DUUMBI maps the failure back to semantic graph context, initially function/block-level and later exact node-level when proven.
5. A repair agent can use that evidence to propose a constrained patch.
6. Validation, rebuild, tests, and a human-reviewable diff gate the repair before it is accepted.

This capability matters because it closes the gap between generated behavior and runtime behavior. Instead of treating a crash as an opaque log, DUUMBI should connect the failure to the graph artifact that represents the intended behavior.

## Intended First Use Case

The first use case is a developer using DUUMBI during development or testing:

- build/run the application in traced mode
- reproduce a runtime failure locally or in CI
- inspect crash evidence and graph back-mapping
- receive a repair-ready context or patch proposal
- accept only after validation and human review

## Explicit Non-Goals For The First Slice

- No automatic repair of deployed customer applications.
- No cloud telemetry ingestion into a DUUMBI account.
- No silent auto-update of end-user installations.
- No hot-swap of running binaries.
- No repair agent action without validation and human-reviewable evidence.

These can become later product tracks only after privacy, consent, account identity, artifact upload, release delivery, rollback, and operational safety are designed.

## Planning Guidance For Developers And Agents

- Treat this as a Phase 13 planned capability, not delivered product behavior.
- Build the telemetry/back-mapping proof before repair automation.
- Prefer a narrow chain: traced build -> runtime failure -> crash dump -> graph evidence -> repair context -> validation -> human review.
- Keep production/customer crash collection out of scope unless a separate accepted product spec exists.
- Keep repair proposals constrained by graph validation, rebuild, test output, and explicit human acceptance.

## Sources

- [[DUUMBI - PRD]]
- [[DUUMBI - Service and Research Direction]]
- [[DUUMBI - Phase 13 - Self-Healing & Telemetry]]
