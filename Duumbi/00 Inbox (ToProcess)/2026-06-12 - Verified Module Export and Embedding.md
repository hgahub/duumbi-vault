---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M5
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Verified Module Export and Embedding

## Context

*Proposed addition (Claude, 2026-06-12).* Nobody rewrites a working system in a new paradigm — adoption dies there. The realistic path: build the **critical 5%** in DUUMBI (the function where money, safety, or legal liability lives — interest calculation, dosage limits, control thresholds, contract clauses) and embed it in existing software as a library. Cranelift already emits native code; what's missing is a stable boundary. This turns DUUMBI from "a new way to build programs" into "a way to put a mathematically verified kernel inside the software you already have" — the single strongest adoption wedge for the verification story, and how I would introduce DUUMBI into any existing codebase I work on.

## Goal

`duumbi export` packages a module as an embeddable artifact — C ABI static/dynamic library with generated headers, and a WASM build — with its contracts enforced or documented at the boundary, callable from Rust, C, JS/TS, Python hosts.

## Subtasks

1. C ABI export: stable `extern "C"` surface generated from exported function signatures (i64/f64/bool/string/array marshalling rules); static + dynamic library output; generated `.h` header with contract documentation in comments.
2. WASM target: compile the same graph to WASM (Cranelift supports it) for browser/edge/plugin embedding; JS/TS binding package.
3. Boundary contract enforcement: optional runtime precondition checks at the FFI boundary (host inputs are untrusted — proofs hold only inside the contract domain); document the trust model precisely.
4. Host bindings: idiomatic wrapper generation — Rust crate first, then npm package; Python wheel as stretch.
5. Artifact provenance: exported libraries carry the graph snapshot id, semantic hash, and verification status — checkable against the registry ([[2026-06-12 - Registry Graph Database Evolution]]).
6. Reference integrations: a verified pricing/limit function called from (a) a Rust service, (b) a TypeScript app via WASM — shipped as examples and docs.
7. Versioning/compat policy for exported ABI across DUUMBI releases.

## Acceptance criteria

- A verified stdlib-style function is callable from a Rust host and a browser JS app, with published, checksummed artifacts.
- Boundary violations (precondition breach from host) fail safely and observably, per the documented trust model.
- Export artifacts are reproducible from the graph snapshot (bit-for-bit or attested).

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
- [[2026-06-12 - Certification Evidence Export]]
- [[2026-06-12 - Verified Business Rules Vertical]]
