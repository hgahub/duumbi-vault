---
tags:
  - project/duumbi
  - dot/product-capability
  - capability/runtime-feedback
status: active
created: 2026-05-21
updated: 2026-05-23
related_maps:
  - "[[DUUMBI Core Concepts Map]]"
  - "[[DUUMBI Technical Architecture Map]]"
related_works:
  - "[[DUUMBI - PRD]]"
  - "[[DUUMBI - Service and Research Direction]]"
---
# Runtime Failure Feedback Loop

## Summary

DUUMBI's runtime failure feedback loop helps a developer diagnose and eventually repair failures that happen while developing, testing, or running a DUUMBI-built application in a controlled developer environment.

Facts:

- The first implemented foundation is local and opt-in: a developer can build with trace instrumentation, run the compiled program, record local trace/crash artifacts, and inspect graph function/block back-mapping evidence.
- The source implementation for the first foundation is linked from issue #580, implementation PR #604, product spec PR #600, technical spec PR #602, and the Stage 12 closure evidence.
- The first foundation preserves default uninstrumented builds and does not add production telemetry ingestion, remote collectors, Studio dashboards, hot-swap, or autonomous repair acceptance.

Decision:

- Treat runtime failure feedback as a local developer/test evidence loop first, not as production self-healing.
- Treat function/block-level graph back-mapping as the first reliable mapping level. Exact node-level mapping remains future work unless a later spec proves enough error context.
- Keep repair acceptance behind graph validation, rebuild, relevant tests, and explicit human review.

Assumptions:

- Local trace/crash artifacts may contain sensitive runtime context, so value capture and upload behavior must remain conservative until separately specified.
- The right product sequence is telemetry/back-mapping evidence first, repair-agent context second, automated proposal generation later, and production repair last.

Open questions:

- What additional graph or runtime context is required before exact node-level mapping is dependable?
- Should a future traced run surface exist in addition to traced build, or is `duumbi build --trace` plus normal run enough?

## Why It Matters

This capability closes the gap between generated behavior and runtime behavior. Instead of treating a crash as an opaque log, DUUMBI connects the failure to the graph artifact that represents the intended behavior.

## DUUMBI Usage

The first local feedback loop is:

1. The developer builds or runs with tracing enabled.
2. The compiled application fails at runtime.
3. DUUMBI records trace and crash evidence locally.
4. DUUMBI maps the failure back to semantic graph context, initially function/block-level and later exact node-level when proven.
5. A repair agent can use that evidence to propose a constrained patch.
6. Validation, rebuild, tests, and a human-reviewable diff gate the repair before it is accepted.

Agents and developers should use this guidance when working on telemetry, runtime, compiler lowering, repair context, patch validation, and user-facing diagnosis flows:

- Default builds must remain uninstrumented.
- Traced behavior must be explicit and local by default.
- Trace maps should preserve graph identity; they must not depend on unstable traversal order.
- Crash evidence should preserve the original runtime failure signal instead of hiding it behind telemetry handling.
- Repair context must be derived from mapped crash evidence, not invented from a generic failure prompt.
- Repair validation evidence may report local gate results, but it must not silently accept or apply a repair.

## Explicit Non-Goals For The First Slice

- No automatic repair of deployed customer applications.
- No cloud telemetry ingestion into a DUUMBI account.
- No silent auto-update of end-user installations.
- No hot-swap of running binaries.
- No repair agent action without validation and human-reviewable evidence.

These can become later product tracks only after privacy, consent, account identity, artifact upload, release delivery, rollback, and operational safety are designed.

## Sources

- [[DUUMBI - PRD]]
- [[DUUMBI - Service and Research Direction]]
- [[DUUMBI - Phase 13 - Self-Healing & Telemetry]]
- Issue #580: https://github.com/hgahub/duumbi/issues/580
- Product spec PR #600: https://github.com/hgahub/duumbi/pull/600
- Technical spec PR #602: https://github.com/hgahub/duumbi/pull/602
- Implementation PR #604: https://github.com/hgahub/duumbi/pull/604
- Stage 12 closure evidence: https://github.com/hgahub/duumbi/issues/580#issuecomment-4525647674

## Related

- [[Compilation Pipeline]]
- [[AI Agent Architecture]]
