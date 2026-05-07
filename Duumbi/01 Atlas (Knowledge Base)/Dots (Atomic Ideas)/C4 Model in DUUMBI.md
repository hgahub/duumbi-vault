---
tags:
  - project/duumbi
  - concept/architecture
status: active
created: 2026-02-08
updated: 2026-05-07
---

# C4 Model in DUUMBI

## Summary

The C4 model gives DUUMBI a layered way to explain architecture: system context, containers, components, and code.

## Why it matters

Agents and humans need architecture context at different levels of detail. C4 keeps high-level product boundaries separate from implementation details.

## DUUMBI usage

- Use system context to explain users, agents, GitHub, Slack, Obsidian, and DUUMBI.
- Use containers to separate CLI, Studio, compiler, graph store, agent layer, and integrations.
- Use components to explain module boundaries before agent implementation.
- Use code-level references only when changing a specific module.

## Sources

- [C4 model](https://c4model.com/)

## Related

- [[DUUMBI Technical Architecture Map]]
- [[Graph Repository Architecture]]
- [[Compilation Pipeline]]
- [[AI Agent Architecture]]
