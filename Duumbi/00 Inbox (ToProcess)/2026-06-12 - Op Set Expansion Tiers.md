---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/critical
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M1
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Op Set Expansion Tiers

## Context

Capability-gap audit (verified 2026-06-12): the ~119-op set covers arithmetic, strings, arrays, structs, ownership, Result/Option, JSON, TCP, HTTP, SQLite, and file I/O — enough for the flagship example, far from "most tasks". Confirmed gaps: **no map/set/dictionary**, no string format/split/join, no string→number parsing, no f64 formatting, **no time/date, no random, no env/args/sleep**, no trig/log/exp, no sorting, **no function values** (no callbacks/comparators — the HTTP server can only serve static routes), no closures, **no generics** (stdlib duplicates per type), no multi-way match/enums, and **loops exist only as backward-branch CFGs** — hard for LLMs to emit correctly and offering no invariant attachment point for verification.

**Design principle:** the op set is also the verification vocabulary. Every new op ships as a complete contract — type rules, ownership rules, WP/VCGen rule, effect annotation, `describe`/projection rendering, and property-test generator — or it doesn't ship. Growing the set carelessly destroys M4.

## Goal

A tiered expansion so the intent pipeline can implement progressively more real tasks, with AI emission ease and verifiability as first-class selection criteria.

## Subtasks

### Tier A — completeness foundations (this milestone)
1. Collections: `Map` op family (string-keyed first: New/Insert/Get/TryGet/Remove/Len/Keys), `Set`; array additions (Pop/Remove/IndexOf/Slice/Sort/Contains) with properly typed elements (i64/f64/string handles).
2. Strings: `StringFormat` (template-based), `Split`/`Join`, `StringToI64`/`StringToF64` → Result, `F64ToString` with precision, byte/char access.
3. Math & system: trig/log/exp/abs/floor/ceil (libm is already linked), `Clock` (monotonic + wall), date formatting, `Random` with **mandatory explicit seed** (determinism/replay — no ambient entropy), `Env`/`Args`, `Sleep`.
4. **Structured Loop op** (`ForRange`/`While` with body block and an explicit invariant slot): desugars to the existing branch CFG for codegen, but exists for three reasons — LLMs emit it far more reliably than backward branches, `describe` output becomes readable, and VCGen gets its loop-invariant attachment point ([[2026-06-12 - Formal Verification VCGen MVP]]).

### Tier B — abstraction (next)
5. Function references + `CallIndirect` with declared signatures → comparators, callbacks, dynamic HTTP route handlers (unblocks a real server story).
6. Explicit closures: capture-struct + function-ref pair, ownership-checked.
7. First-class enums/tagged unions + multi-way `Match` with exhaustiveness checking (today Match is hardcoded binary Ok/Err, Some/None).
8. Generics decision: **graph-level monomorphization** — typed instances generated at the intent/registry layer (fits semantic-hash reuse and keeps codegen monomorphic); document as the chosen strategy vs. per-type stdlib duplication.

### Tier C — systems
9. ExternCall, concurrency, TLS/WebSocket/streaming → scoped in [[2026-06-12 - Runtime Capability Modules and Library Adoption]].

### Process
10. Eval-driven prioritization: mine intent-eval failures ("this intent needed op X") to order the backlog; every new family lands with a showcase, a stdlib wrapper module, and a registry publish.

## Acceptance criteria

- The M1 multi-module eval corpus is expressible without workarounds for: dictionary use, string formatting/parsing, timestamps, seeded randomness, and loop-based iteration.
- Every shipped op family has its VCGen rule, effect annotation, and property-test generator on day one.
- LLM graph-emission failure rate measured before/after the structured Loop op shows a significant drop on iteration-heavy tasks.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Codegen Trap Discipline and Backend Hardening]]
- [[2026-06-12 - Runtime Capability Modules and Library Adoption]]
- [[2026-06-12 - Formal Verification VCGen MVP]]
- [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
