---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/high
created: 2026-06-12
milestone: M6
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Verified Business Rules Vertical (Legal / Financial)

## Context

*Proposed addition (Claude, 2026-06-12).* Legal, tax, insurance, and payroll logic is a natural-language specification (statute, policy, contract) that must be implemented provably correctly, audited for years, and survive both regulation changes and technology churn. Every DUUMBI strength maps onto this 1:1: intent provenance (each function linked to the exact regulation paragraph that mandates it), contracts (legal conditions become pre/postconditions), determinism + append-only ledger (audit), formal verification (the rule provably does what the law says), and **no human programming language** — the graph is self-describing data, immune to language fashion over a statute's decade-long life. This is the flagship vertical that makes "mathematically proven applications for special engineering and legal domains" concrete, and the v1.0 launch story candidate.

## Goal

A pilot regulated ruleset implemented end-to-end in DUUMBI: every function carries a citation to its legal source, key properties are proven or property-tested, the evidence bundle is exportable, and a domain expert signs off — published as the reference case study.

## Subtasks

1. Pick the pilot domain: small, closed, high-value ruleset — candidates: an EU/Hungarian VAT rate determination subset, an insurance claim eligibility ruleset, or payroll contribution calculation. Criteria: bounded inputs, published official test cases, access to a domain expert.
2. Regulation-to-contract methodology: how a statute paragraph becomes an intent + pre/postconditions; document the workflow (this is the reusable product, not just the pilot).
3. Provenance fields: extend intent/graph metadata with legal-source citations (document id, section, version, effective date); regulation version changes flag affected graph regions — "what code does §12(3) govern?" becomes a query.
4. Implement the pilot via the intent pipeline; prove/property-test the critical properties (e.g. rate bounds, exhaustiveness of case analysis, monotonicity where the law implies it).
5. Expert review loop: domain expert validates the contracts against the law (the proofs guarantee code matches contracts; the expert guarantees contracts match the law — state this division of trust explicitly).
6. Deliverables: evidence bundle ([[2026-06-12 - Certification Evidence Export]]), embeddable library ([[2026-06-12 - Verified Module Export and Embedding]]) so an existing billing/HR system can call the verified rules, and a public case study for the v1.0 launch.

## Acceptance criteria

- Pilot ruleset passes the official/published test cases; key properties proven or property-tested with evidence.
- Every function answers "which legal source mandates you?" via query.
- External domain expert validates contract-to-law fidelity in writing; case study published.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Verified Module Export and Embedding]]
- [[2026-06-12 - Certification Evidence Export]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
