---
tags:
  - project/duumbi
  - map/roadmap
status: active
created: 2026-03-12
updated: 2026-05-06
---
# DUUMBI -- Development Roadmap (Hub)

> Master overview. Every milestone links to its own note. Status reflects GitHub issues, milestones, and actual completion. Current product roadmap: [[DUUMBI - Product Roadmap 2026-05]].

---

## Status Summary

| Phase | Title | GitHub | Status |
|-------|-------|--------|--------|
| 0 | Proof of Concept | 8/8 | Done |
| 1 | Usable CLI | 12/12 | Done |
| 2 | AI Integration | 13/13 | Done |
| 3 | Web Visualizer | 9/9 | Done |
| 4 | Interactive CLI & Module System | 16/16 | Done |
| 5 | Intent-Driven Development | 22/22 | Done |
| 6 | DUUMBI Studio | 47/47 | Done |
| 7 | Registry & Distribution | 37/37 | Done |
| 8 | Registry Auth & User Management | 23/23 | Done |
| 9a | Type System Completion (9a-1/2/3) | 52/52 | Done |
| 9A | Stdlib & Instruction Set | 16/16 | Done |
| 9B | Multi-LLM Providers | 13/13 | Done |
| 9C | Benchmark & Showcases | 10/10 | Done |
| 10 | Intelligent Context & Knowledge Graph | 26/26 | Done |
| 11 | CLI UX & Developer Experience | 35/35 | Done |
| 12 | Dynamic Agent System & MCP | 46/46 | Done |
| 13 | Self-Healing & Telemetry | no open `phase-13` issues found | Planned |
| 14 | Marketing & Go-to-Market | open GTM/ecosystem issues remain | In Progress |
| 15 | Studio Workflow Redesign | 10/13 | Partial: #487, #488, #489 open |
| 16 | Windows & Cross-Platform Support | 1/4 (#420-#423) | Planned/partial: #420, #422, #423 open |

**Current delivery focus:** Phase 15 -- Studio Workflow Redesign. #486 closed on 2026-05-03; #487, #488, and #489 remain open.

---

## Dependency Graph

```text
Phase 0-12 (delivered foundation)
    |
    +--> Phase 15: Studio Workflow Redesign (active, 10/13)
    |       |
    |       +--> Phase 13: Self-Healing & Telemetry (planned; create issue-backed scope first)
    |
    +--> Phase 16: Windows & Cross-Platform Support (planned/partial, 1/4)
    |
    +--> Phase 14: Marketing & Go-to-Market (parallel, evidence-backed launch work)
```

---

## Current Roadmap

- [[DUUMBI - Product Roadmap 2026-05]] -- current roadmap snapshot and sequencing.
- [[DUUMBI - PRD]] -- current product strategy and architecture.

The near-term sequence is:
1. Finish Phase 15 E2E samples and documentation.
2. Complete Phase 16 Windows CI/docs.
3. Create issue-backed Phase 13 self-healing scope.
4. Continue Phase 14 marketing with evidence-backed claims.
5. Specify shared session kernel, append-only event ledger, runner abstraction, and multi-surface architecture.

---

## Milestone Notes

### MVP (Phase 0-3)

- [[DUUMBI - Phase 0 - Proof of Concept]] -- JSON-LD to Cranelift to native binary.
- [[DUUMBI - Phase 1 - Usable CLI]] -- CLI commands, fibonacci, f64/bool.
- [[DUUMBI - Phase 2 - AI Integration]] -- `duumbi add`, undo, benchmark.
- [[DUUMBI - Phase 3 - Web Visualizer]] -- Cytoscape.js, axum, WebSocket.

### Post-MVP Foundation (Phase 4-8)

- [[DUUMBI - Phase 4 - Interactive CLI & Module System]] -- REPL, module system, stdlib.
- [[DUUMBI - Phase 5 - Intent-Driven Development]] -- Intent spec, Coordinator, Verifier.
- [[DUUMBI - Phase 6 - DUUMBI Studio]] -- Leptos SSR, C4 drill-down, chat UI.
- [[DUUMBI - Phase 7 - Registry & Distribution]] -- publish, install, lockfile v1.
- [[DUUMBI - Phase 8 - Registry Auth & User Management]] -- OAuth, tokens, device code flow.

### Type System & Build (Phase 9a-9C)

- [[DUUMBI - Phase 9a - Type System Completion]] -- String, Array, Struct, ownership/lifetimes.
- [[DUUMBI - Phase 9 - Build Excellence & Multi-LLM]] -- stdlib, multi-provider, benchmarks, showcases.

### Intelligence & UX (Phase 10-11)

- [[DUUMBI - Phase 10 - Intelligent Context & Knowledge Graph]] -- MutationOutcome, `/resume`, `/knowledge`, auto-context.
- [[DUUMBI - Phase 11 - CLI UX & Developer Experience]] -- CLI polish, completions, tables, fuzzy matching.

### Agent Platform (Phase 12) + Self-Healing (Phase 13)

- [[DUUMBI - Phase 12 - Dynamic Agent System & MCP]] -- MCP server, dynamic agents, cost control, merge engine.
- [[DUUMBI - Phase 13 - Self-Healing & Telemetry]] -- planned; no open `phase-13` issues found during 2026-05-04 refresh.

### Studio Workflow (Phase 15)

- [[DUUMBI - Phase 15 - Studio Workflow Redesign]] -- 3-panel design, WebSocket chat, REPL fixes, spinner, agent templates, E2E evidence.

Open Phase 15 work:
- #487 String Utilities E2E.
- #488 Math Library E2E.
- #489 E2E protocol documentation.

### Go-to-Market (Phase 14)

- [[DUUMBI - Phase 14 - Marketing & Go-to-Market]] -- content, community, ecosystem, launch execution.

### Cross-Platform (Phase 16)

- [[DUUMBI - Phase 16 - Windows & Cross-Platform Support]] -- Windows CI, path audit, terminal color test, README requirements.

Open Phase 16 work:
- #420 Windows CI matrix.
- #422 Windows terminal color fallback test.
- #423 Windows requirements in README.

Closed Phase 16 work:
- #421 path separator audit.

---

## Kill Criterion Summary

| Phase | Kill Criterion | Result |
|-------|----------------|--------|
| 0 | `add(3,5)` -> binary prints `8` | Done |
| 1 | External dev installs and runs in < 10 min | Done |
| 2 | >70% correct on 20-command benchmark | Done: 20/20 |
| 3 | 3/3 devs confirm faster than raw JSON-LD | Done |
| 4 | `abs(-7) = 7` via init -> 2-module -> binary | Done |
| 5 | `double(21)=42` via intent pipeline | Done |
| 6 | 3/3 devs faster navigation web vs CLI | Done |
| 7 | Deterministic hash + publish/install + offline build | Done |
| 8 | GitHub OAuth -> token -> device code flow login | Done |
| 9a | String + Array + ownership validation rejects use-after-move | Done |
| 9 | 5 showcases x 2+ LLM providers >=95% | Done |
| 10 | Existing graph recognition + modular add + auto-context | Done |
| 11 | 3/3 new users succeed via `/` menu in 10 min and `/resume` works | Done |
| 12 | External MCP client -> graph mutation + dynamic agent team | Done |
| 15 | 3 sample tasks in CLI REPL + Studio 3-panel + real LLM chat | Partial: #486 done; #487/#488/#489 open |
| 13 | Runtime error -> nodeId -> valid patch -> tests green | Planned |
| 14 | duumbi.dev live + 3 demos + article + 200 stars | In progress |
| 16 | `cargo test --all` passes on `windows-latest` CI + Windows install works | Planned/partial |

---

## Future Product Directions

| Direction | Description |
|---|---|
| Shared session kernel | One session model across CLI, TUI, Studio, desktop, and chat bridges |
| Append-only event ledger | Patch, checkpoint, diagnostic, build/run, approval, and artifact events |
| Runner abstraction | Local, CI, and future remote execution through one contract |
| Studio web/desktop | Shared Studio frontend, later packaged for desktop |
| Thin chat bridges | Slack/Discord for status, assignment, approvals, and deep links |
| Enterprise control plane | Auth, policy, audit, model routing, registry metadata, isolated runners |
| LLVM backend | Inkwell/LLVM path for broader target support |
| Projectional editing | Graph to pseudo-code to diagram view switching |
| Diagram-driven development | C4 diagrams to graph skeletons |
| IDE plugin | VS Code schema autocomplete and live graph preview |
| Code import | Python/JS/Rust to JSON-LD graph conversion |

---

## Related Documents

- [[DUUMBI - Product Roadmap 2026-05]] -- current roadmap snapshot
- [[DUUMBI - PRD]] -- current product strategy and long-term vision
- [[DUUMBI Core Concepts Map]] -- conceptual overview
- [[DUUMBI - MVP Specification]] -- original Phase 0-3 specification
- [[DUUMBI - Post-MVP Roadmap]] -- older business plan and monetization strategy
- [[DUUMBI - Graph Repository Architecture]] -- Phase 7 architecture
- [[DUUMBI - Architecture Diagram]] -- technical architecture
- [[DUUMBI - Tools and Components]] -- tech stack
- [[DUUMBI - Glossary]] -- terminology
- [[Slack as Thin Surface; GitHub + Obsidian as Sources of Truth]] -- operating rule for where roadmap state and architecture knowledge should live


> [!important] **Timeline update (2026-03-14):** Phase 9a estimate increased to 10–14 weeks (from 8–12) due to addition of Result/Option error handling system. Total sequential estimate: **~48–62 weeks for Phase 9a–13.**


> [!important] **Kill Criterion update (2026-03-14):** Phase 9 kill criterion refined: "flawlessly" replaced with "≥95% success rate (19/20 runs)" per showcase per provider. Phase 14 GitHub star target increased from 50 to 200 (within 30 days of HN launch).


> [!note] **Phase 15+ addition (2026-03-14):** Added "15g: General Enum Type" — user-defined tagged unions with arbitrary variants, pattern matching, exhaustiveness checking. Deferred from Phase 9a where only Result/Option (2-variant) tagged unions exist.


> [!important] **Scope update (2026-03-14):** Phase 9 estimate increased from 8–10 weeks to **10–14 weeks** due to addition of File I/O stdlib, JSON parse/serialize, TCP socket, and HTTP client. Phase 9a also adds: multi-language projection (`--lang rust/python/go`), two-phase generation strategy, LLM-friendly validator error messages, and automatic prompt evolution system. **Revised total sequential estimate: ~52–70 weeks for Phase 9a–13.**


> [!important] **Major scope update (2026-03-15):** Three critical additions to close the production-readiness gap:
> 1. **Phase 9a: FFI (Foreign Function Interface)** — unsafe ExternFunction + UnsafeBlock + RawPointer. The entire C library ecosystem becomes accessible. E050–E053 validator rules.
> 2. **Phase 9: TLS (LibreSSL/OpenSSL) + SQLite (vendored) + Event Loop (poll-based multi-connection server).** The showcase #4 is now a production-viable SMTP server with TLS, database, and concurrent connections.
> 3. **Phase 14: Ecosystem Seed Strategy** — 10 Tier 1 stdlib modules pre-installed, 10 Tier 2 within 3 months. FFI makes wrapping C libraries a 1–3 day task instead of weeks.
>
> **Revised time estimates:** Phase 9a: 10–14 wks (unchanged). Phase 9: 12–16 wks (was 10–14). **Total sequential: ~54–74 weeks for Phase 9a–13.**
>
> **Why this matters:** These three changes transform DUUMBI from "demo-grade calculator" to "production-viable tool with TLS, database, multi-connection server, and access to the C ecosystem via FFI."


> [!success] **Execution update (2026-03-17):** Phase 8 is fully complete (`#194`–`#215`, 22/22 issues closed). Phase 9a has started in GitHub: milestone **Phase 9a-1: Heap Types & Runtime** closed all 18 tracked issues (`#227`–`#244`, including integration kill criterion `#243`), and the next active item is **#240 Heap memory management: Drop insertion at block scope exits** in milestone **Phase 9a-2: Ownership & Lifetimes**.


> [!success] **Execution update (2026-03-17 — Phase 9a complete):** Milestone **Phase 9a-2: Ownership & Lifetimes** is fully closed — all 19 issues (`#240`, `#250`–`#267`) merged in PR [#268](https://github.com/hgahub/duumbi/pull/268). Delivered: `Op::Alloc/Move/Borrow/Drop`, `DuumbiType::Ref(&T)/RefMut(&mut T)`, E020–E029 ownership validator, automatic LIFO drop insertion, 890 tests (62 new), 6 JSON-LD fixtures. **Phase 9a is now 100% complete (37/37 issues across both sub-milestones).** Kill criterion satisfied: validator rejects use-after-move (E021), double-borrow-mut (E022), and dangling reference (E026) at graph validation time. Next active phase: **Phase 9 — Build Excellence & Multi-LLM.**


> [!note] **Phase 15 update (2026-03-15):** Phase 15a (LLVM Backend) expanded to include **WASM compilation target** (`duumbi build --target wasm32`). WASM enables: browser-based DUUMBI playground (onboarding), Figma plugin generation (with MCP client from Phase 12), Cloudflare Workers / edge computing deployment. Requires WASI-compatible runtime shim (replaces libc-based C shim). Phase 12 also gained **MCP Client capability** — agents can call external MCP servers (Figma, GitHub, Browser, Database) alongside internal DUUMBI tools.
>
> Updated Phase 15 table:
> | # | Direction | Description |
> |---|-----------|-------------|
> | 15a | **LLVM Backend + WASM Target** | Inkwell → LLVM IR → x86/aarch64/WASM. WASI runtime shim. Browser playground. Figma plugin target. |
> | 15b | **Projectional Editing** | Graph ↔ code view (bidirectional editing, not read-only `--lang`) |
> | 15c | **Diagram-Driven Development** | Draw C4 diagrams → automatic graph generation |
> | 15d | **DUUMBI Playground** | Browser-based WASM compiler + visualizer (depends on 15a WASM) |
> | 15e | **IDE Plugin** | VS Code extension: schema autocomplete, live graph preview |
> | 15f | **Code Import** | Python/JS/Rust → JSON-LD graph conversion |
> | 15g | **General Enum Type** | User-defined tagged unions, N-variant pattern matching |
> | 15h | **Concurrency** | Thread spawn, channels, Mutex/Arc in generated programs |


> [!important] **Formal Verification addition (2026-03-15):**
> - **Phase 9a:** Added optional `duumbi:precondition`, `duumbi:postcondition`, `duumbi:loopInvariant`, `duumbi:loopVariant`, `duumbi:verificationStatus` fields to the JSON-LD schema. Zero implementation cost — stored but not verified yet. LLMs can generate them, developers can write them.
> - **Phase 15i (NEW): Formal Verification** — VCGen from JSON-LD graph → Z3 SMT solver → proof certificate or counterexample. `duumbi verify` command. Verification Agent (Phase 12 extension). The thesis: formal verification on semantic graphs eliminates the parsing/AST/SSA pipeline that makes traditional formal verification impractical. Three stdlib functions (abs, max, sort invariant) as kill criterion.
> - **Scientific significance:** DUUMBI would be the **only tool that AI-generates code AND formally verifies it.** Dafny/SPARK/Lean require manual code + manual specifications. DUUMBI generates both. Four publishable research contributions identified.
>
> Updated Phase 15 table:
> | # | Direction | Description |
> |---|-----------|-------------|
> | 15a | **LLVM Backend + WASM Target** | Inkwell → LLVM IR → x86/aarch64/WASM. WASI runtime shim. |
> | 15b | **Projectional Editing** | Graph ↔ code (bidirectional editing) |
> | 15c | **Diagram-Driven Development** | Draw C4 diagrams → automatic graph generation |
> | 15d | **DUUMBI Playground** | Browser WASM compiler (depends on 15a) |
> | 15e | **IDE Plugin** | VS Code: schema autocomplete, live graph preview |
> | 15f | **Code Import** | Python/JS/Rust → JSON-LD conversion |
> | 15g | **General Enum Type** | User-defined tagged unions, N-variant matching |
> | 15h | **Concurrency** | Thread spawn, channels, Mutex/Arc |
> | 15i | **Formal Verification** | VCGen → Z3 → proof certificates. AI-generated specs. Verification Agent. |
