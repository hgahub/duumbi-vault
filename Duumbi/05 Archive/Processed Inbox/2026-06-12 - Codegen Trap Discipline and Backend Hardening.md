---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Codegen Trap Discipline and Backend Hardening

## Context

Backend audit (verified 2026-06-12): the graph → Cranelift → object → linker pipeline is architecturally sound — ~27 scalar ops lower to native CLIF instructions, ~93 heap-type ops call the embedded C runtime (`runtime/duumbi_runtime.c`, ~4,000 lines, links libcurl/SQLite3/libm/sockets), with SSA value mapping, LIFO auto-drop, recursion and cross-module symbol mangling all working. But the **error semantics contradict the verifiability story**:

- `ArrayGet` out-of-bounds **silently returns 0** (wrong answers, no signal) while `ArraySet` panics — inconsistent and dangerous.
- `ArrayTryGet` exists in the Op enum but is **unimplemented at codegen** (`src/compiler/lowering.rs:1589`) — the safe alternative doesn't exist.
- Division by zero lowers to raw `sdiv` → OS SIGFPE; the process dies with no message, no node attribution, no telemetry.
- Integer overflow wraps silently with no documented policy — while the VCGen plan emits overflow obligations.
- Cranelift IR verifier not enabled; struct layout is a fixed 64-byte buffer with sequential 8-byte untyped fields; panic = `exit(1)`; host-triple compilation only.

## Goal

Every runtime failure is defined, deterministic, and attributed to a graph node id; the backend's correctness is continuously checked; semantics are consistent with what the verifier will later prove.

## Subtasks

1. Trap discipline: division guarded (panic with node id, plus `DivChecked` → Result variant for verified code); `ArrayGet` OOB → node-attributed panic, never silent 0; implement `ArrayTryGet` → Option end-to-end; define and document the integer overflow policy (default documented wrap + checked variants whose obligations VCGen understands).
2. Panic provenance: the panic path carries function/block/node ids in release builds too (telemetry trace-id mapping exists behind `--trace`; make minimal attribution unconditional).
3. Enable the Cranelift IR verifier in debug/CI builds; add codegen property tests per op family.
4. Reference semantics oracle: a small graph interpreter used for differential testing (random graphs: interpreter vs native output must match) — doubles as the semantics ground truth for [[2026-06-12 - Formal Verification VCGen MVP]] and the determinism replay harness.
5. Struct layout registry: type-aware field sizes and alignment; remove the 8-field/64-byte cap (the Phase 9a-2 TODO in `lowering.rs:1619`).
6. Cross-compilation groundwork: target-triple override (Cranelift is cross-capable; document per-target linking strategy) — feeds the prebuilt-binary release matrix and the later WASM target of [[2026-06-12 - Verified Module Export and Embedding]].

## Acceptance criteria

- Div-by-zero and array OOB produce deterministic, node-attributed error messages (test-covered on all platforms); no op silently returns wrong data.
- `ArrayTryGet` works end-to-end with Match.
- Differential interpreter-vs-native tests run in CI; Cranelift verifier active in CI.
- A struct with 12+ mixed-type fields compiles and lays out correctly.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Op Set Expansion Tiers]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
- [[2026-06-12 - Determinism Program for AI Development]]

## Triage result
- Date: 2026-06-14T20:33:12.139Z
- Classification: execution work
- Routing: Created GitHub issue #714 and routed it to Needs Human Acceptance.
- GitHub artifacts:
  - https://github.com/hgahub/duumbi/issues/714
- Obsidian artifacts:
  - none
- Canonical duplicate:
  - none
- Open questions:
  - See GitHub issue.
- Assumptions:
  - Automated triage refill selected this source as actionable. Rationale: Codegen reliability and deterministic error semantics are critical for a credible developer preview; this is the highest-priority unaddressed M1 item from the inbox notes.
- Next stage: Needs Human Acceptance
