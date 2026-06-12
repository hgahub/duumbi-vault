---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M4
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Certification Evidence Export

## Context

*Proposed addition (Claude, 2026-06-12).* In safety- and assurance-critical engineering (aerospace DO-178C, industrial IEC 61508, automotive ISO 26262, medical IEC 62304), the dominant cost is not writing code — it is producing and maintaining the **traceability and verification documentation**: requirement ↔ design ↔ code ↔ test ↔ result matrices, kept consistent by hand. DUUMBI stores exactly these links natively (intent → spec/BDD → graph nodes → tests → property/proof results → session ledger), so the audit dossier becomes an **export**, not a project. This is a use case where I would genuinely choose DUUMBI over any text-language toolchain: the evidence is a by-product of how the system works, never stale, never hand-assembled.

## Goal

One command produces an audit-ready evidence bundle for a module or program: traceability matrix, verification results (tests, property runs, formal proofs with prover versions), change history from the ledger, and an explicit gap report listing every unproven or untested claim.

## Subtasks

1. Traceability matrix export: requirement/intent ↔ graph node ↔ BDD scenario ↔ test/property/proof result, generated from existing links; identify and close any missing link types in the data model.
2. Evidence bundle format: human-readable (HTML/PDF) + machine-readable (JSON) with content hashes, tool versions (compiler, prover), and snapshot ids — reproducible and tamper-evident.
3. Gap report: every exported claim is classified proven / property-tested / tested / asserted-only; unverified surface is listed explicitly (honest by default — this is the differentiator, not a marketing gloss).
4. Standards mapping research: with a domain expert, map bundle sections onto the artifact requirements of one chosen standard (start with the most achievable, e.g. IEC 61508 software support or a DO-178C subset); document what DUUMBI can and cannot claim.
5. Pilot: run the export on a verified stdlib module + the flagship example; iterate format with someone who has lived through a real certification audit.
6. GTM: "compliance dossier as a build artifact" page + the [[2026-06-12 - Verified Business Rules Vertical]] story share this foundation.

## Acceptance criteria

- `duumbi evidence export <module>` (name TBD) produces a complete bundle covering 100% of exported functions, with the gap report.
- Bundle regenerated after a code change shows precisely the impacted rows (delta view).
- A domain expert review confirms the bundle maps credibly onto at least one real standard's artifact list.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
- [[2026-06-12 - Contract Property Test Generation]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
