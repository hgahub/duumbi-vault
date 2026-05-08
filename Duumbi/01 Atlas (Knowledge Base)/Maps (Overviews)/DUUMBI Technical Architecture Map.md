---
tags:
  - project/duumbi
  - map/technical-architecture
status: active
created: 2026-02-08
updated: 2026-05-08
---

# DUUMBI Technical Architecture Map

This map points to the durable architecture concepts agents and developers need before changing code. It is not an implementation status page.

## Architecture Viewpoints

- [[DUUMBI - Service and Research Direction]] -- service framing, maturity matrix, query surface, build priorities, and research direction.
- [[DUUMBI Repository Map]] -- repository ownership and cross-repo boundaries.
- [[C4 Model in DUUMBI]] -- system, container, component, and code viewpoints for explaining architecture.
- [[Graph Repository Architecture]] -- storage, module identity, namespace, and resolution principles.
- [[JSON-LD Graph Representation]] -- linked-data substrate for semantic graph documents.
- [[Compilation Pipeline]] -- validated graph to executable output.
- [[AI Agent Architecture]] -- product agent layer, runtime context, and review evidence.
- [[DUUMBI Registry Architecture]] -- module registry API, auth, storage, and UI boundaries.
- [[DUUMBI Azure Infrastructure Model]] -- DNS, static sites, registry hosting, secrets, logging, and persistence.

## Development Contracts

- Repository `AGENTS.md` in `hgahub/duumbi` defines repo-specific agent instructions.
- Source code, tests, CI, and PR review remain authoritative for implementation behavior.
- Obsidian explains durable concepts and workflows, but does not override code or CI.
- GitHub Project tracks current work state; this map links only stable architecture guidance.
- Read-only question answering should cite source, graph, registry, test, runtime, or knowledge evidence before recommending mutation.
- Use [[Visual Documentation in Obsidian]] when a Mermaid or Excalidraw diagram makes architecture easier to inspect.

## Agent-Ready Architecture Checklist

Before assigning architecture work to an agent, provide:

- intended user-visible behavior
- expected read-only answers: what exists, why it exists, where it lives, what depends on it, what proves it, and what risk a change carries
- affected graph concepts and source modules
- invariants that must not change
- compatibility and migration expectations
- expected tests, screenshots, logs, or structured evidence
- required updates to Dots, Maps, skills, or repo `AGENTS.md`

## Related

- [[DUUMBI - PRD]]
- [[DUUMBI - Service and Research Direction]]
- [[DUUMBI - Glossary]]
- [[DUUMBI Repository Map]]
- [[DUUMBI Agentic Development Map]]
- [[Spec-First Agentic Development]]
- [[Structured Agent Review Artifacts]]
