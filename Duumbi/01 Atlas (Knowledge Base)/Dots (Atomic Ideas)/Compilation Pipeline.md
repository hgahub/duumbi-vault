---
tags:
  - project/duumbi
  - concept/compiler
status: active
created: 2026-02-08
updated: 2026-05-07
---

# Compilation Pipeline

## Summary

DUUMBI's compilation pipeline transforms validated semantic graph input into executable output through explicit, testable boundaries.

## Why it matters

The graph-to-code path is where product intent becomes runtime behavior. Agents need clear boundaries so they can change one layer without silently breaking another.

## DUUMBI usage

- Validate graph structure before code generation.
- Keep node identity traceable through parsing, validation, lowering, code generation, and runtime evidence.
- Treat compiler backend changes as high-risk unless tests prove behavior and invariants remain stable.
- Use architecture Dots and repo `AGENTS.md` to define expected validation commands before implementation.

## Sources

- [Cranelift](https://cranelift.dev/)
- [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/)

## Related

- [[JSON-LD Graph Representation]]
- [[Graph Repository Architecture]]
- [[DUUMBI Technical Architecture Map]]
- [[Structured Agent Review Artifacts]]
