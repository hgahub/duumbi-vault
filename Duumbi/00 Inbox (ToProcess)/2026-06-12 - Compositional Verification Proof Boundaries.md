---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/research
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/high
created: 2026-06-12
milestone: M4
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Compositional Verification: Proof Boundaries

## Context

[[DUUMBI - Service and Research Direction]] hypothesis: registry module boundaries and semantic hashes can serve as **proof cache boundaries** — verify a module once against its contract, then reuse the proof wherever the semantic hash matches, instead of re-verifying whole programs. Depends on [[2026-06-12 - Formal Verification VCGen MVP]] (contracts must exist) and on the semantic-hash index in [[2026-06-12 - Registry Graph Database Evolution]].

## Goal

Calls across module boundaries verify against the callee's published contract (assume-guarantee), and proofs are cached/keyed by semantic hash so verification scales with changed code, not program size.

## Subtasks

1. Module contract format: exported function signatures + pre/postconditions + effect summary, packaged with the module (registry metadata + graph fields).
2. Assume-guarantee VCGen: at a `Call` node, use the callee contract instead of inlining the callee body; emit an obligation that the callee satisfies its own contract (proved once, separately).
3. Proof cache: store proof results keyed by (semantic hash, contract hash, prover version) in the graph-aware `duumbi-registry`; invalidate on either hash change.
4. Trust model: who may publish "verified" claims; re-verification policy on download; signed proof artifacts (long-term).
5. Demonstrate on stdlib: verify `stdlib-math` functions once; verify a consumer program using only the cached contracts; measure verification time vs. monolithic VCGen.
6. Define honest failure modes: contract too weak (consumer obligation unprovable) → actionable error pointing at the missing guarantee.

## Acceptance criteria

- A program using verified stdlib modules verifies without re-proving stdlib bodies.
- Proof cache hit/miss is observable; changing a module body with the same contract invalidates only that module's proof.
- Research note documenting whether the proof-boundary hypothesis holds, with measurements.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
- [[2026-06-12 - Registry Graph Database Evolution]]
