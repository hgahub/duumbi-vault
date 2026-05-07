---
tags:
  - project/duumbi
  - concept/json-ld
status: active
created: 2026-02-08
updated: 2026-05-07
---

# JSON-LD Graph Representation

## Summary

JSON-LD gives DUUMBI a JSON-compatible way to represent linked semantic data with stable identifiers and graph relationships.

## Why it matters

DUUMBI needs program structure to be machine-readable, linkable, and compatible with existing JSON tooling. JSON-LD supports that without requiring every tool to become RDF-native.

## DUUMBI usage

- Use JSON-LD for graph documents that need semantic identity.
- Keep contexts and identifiers explicit.
- Preserve node identity across validation, transformation, compilation, and review evidence.
- Prefer deterministic graph structures that agents can inspect and patch safely.

## Sources

- [JSON-LD 1.1](https://www.w3.org/TR/json-ld11/)

## Related

- [[Graph Repository Architecture]]
- [[Compilation Pipeline]]
- [[Semantic Fixed Point]]
- [[DUUMBI Technical Architecture Map]]
