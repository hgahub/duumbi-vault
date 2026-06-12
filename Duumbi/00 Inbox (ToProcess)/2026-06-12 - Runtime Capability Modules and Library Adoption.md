---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M2
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Runtime Capability Modules and Library Adoption

## Context

The runtime today is one embedded ~4,000-line C file linking libcurl, SQLite3, libm, and platform sockets; user graphs have **no FFI** — capabilities exist only as hand-written op families. That closure is a feature (full validation, no arbitrary native calls) but hand-writing every capability doesn't scale. The pattern is already proven twice: SQLite and HTTP were wrapped behind op families + Tier 1 stdlib modules on the registry. This note industrializes that pattern so mature external libraries become registry-distributed "batteries".

## Goal

A capability-module architecture: vetted external libraries wrapped behind typed, effect-annotated boundaries, packaged as versioned runtime capabilities + registry stdlib modules with contracts and evidence — validation closure preserved, batteries included.

## Subtasks

1. Runtime modularization: split the monolithic runtime into core + per-capability objects (`.o`/`.a`); the workspace manifest (capabilities section + registry deps) drives the link line. A workspace that doesn't use TLS doesn't link TLS.
2. `ExternCall` design: each capability ships a **declared extern signature manifest** (typed params/returns, effect annotation, error contract); the validator checks every call against it. No arbitrary user FFI — capabilities are reviewed, checksummed registry artifacts, and a workspace must explicitly enable each one (permission model, consistent with existing file/net sandboxing).
3. Adoption shortlist (C ABI directly, or Rust crates via `staticlib` + `extern "C"` — links into the existing pipeline unchanged):
   - **rustls** via rustls-ffi — TLS / HTTPS raw sockets (closes the explicitly deferred TLS module),
   - **regex**: `rure` (the rust regex crate's official C API) or PCRE2,
   - **libsodium** — hashing, HMAC, signatures, secure random (audited, small),
   - **zstd / zlib** — compression,
   - **time/date**: libc + strftime in core runtime (or Rust `time` via FFI if needed),
   - base64/hex/uuid: trivial, implement in core runtime,
   - optional later: **yyjson** to replace the custom JSON parser if performance demands.
4. **Java verdict: do not embed a JVM.** JNI/JVM linkage contradicts the single-binary, deterministic, lightweight runtime; every shortlisted need has a C/Rust equivalent. JVM ecosystems integrate across a process/network boundary (the existing HTTP/TCP ops), not via linkage.
5. Registry distribution: a capability module = runtime artifact (per-target prebuilt static lib + checksums + license metadata) + stdlib graph wrapper with contracts + evidence (tests, provenance); the registry serves both halves (extends [[2026-06-12 - Registry Graph Database Evolution]]).
6. Verification trust model: extern functions are **trusted-boundary axioms** — their contracts are asserted, not proven. [[2026-06-12 - Compositional Verification Proof Boundaries]] and the gap report in [[2026-06-12 - Certification Evidence Export]] must label them explicitly as trusted.
7. Supply-chain hygiene: vendored library versions, license inventory (MPL-2.0 compatibility), reproducible builds with checksums.

## Acceptance criteria

- At least two capability modules (TLS, regex) installable from the registry into a clean workspace and callable from user graphs, with validator-enforced signatures.
- A graph calling a capability the workspace hasn't enabled fails at **validation**, not at runtime.
- Registry pages show evidence, checksum, and license metadata for capability modules.
- Core binary size unchanged for workspaces that enable no capabilities.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Op Set Expansion Tiers]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Compositional Verification Proof Boundaries]]
