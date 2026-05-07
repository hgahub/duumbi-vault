---
tags:
  - project/duumbi
  - map/overview
status: active
created: 2026-03-12
updated: 2026-05-04
---
# DUUMBI Core Concepts Map

> This map connects the foundational ideas that define DUUMBI. Start here to understand what DUUMBI is, how its pieces fit together, and which product documents are current.

## The Central Thesis

DUUMBI is an AI-first semantic graph compiler. Software is represented as a JSON-LD graph, not text files. AI generates structured data, not character sequences. What can be validated by schema cannot be hallucination.

The current product direction is multi-surface but single-product: CLI, interactive CLI/TUI, Studio, future desktop, and future chat bridges should share the same graph kernel, session model, event ledger, runner abstraction, and policy boundaries.

## Core Concept Notes (Dots)

### Representation
- [[JSON-LD Graph Representation]] -- why JSON-LD, how ops map to IR instructions
- [[C4 Model in DUUMBI]] -- how C4 architecture layers map to graph layers

### Compilation
- [[Semantic Fixed Point]] -- the central invariant: valid schema + passing tests + fulfilled intent
- [[Compilation Pipeline]] -- JSON-LD to Cranelift IR to native binary, no intermediate language

### Development Workflow
- [[Intent-Driven Development]] -- developer defines intent, AI generates graph patches
- [[AI Agent Architecture]] -- dynamic agents and MCP as the delivered orchestration foundation

### Product Architecture
- [[DUUMBI - PRD]] -- current product vision, architecture strategy, and multi-surface product model
- [[DUUMBI - Product Roadmap 2026-05]] -- current roadmap snapshot based on GitHub issue state
- [[DUUMBI Roadmap Map]] -- roadmap hub and milestone links

### Infrastructure
- [[Graph Repository Architecture]] -- three-layer module storage (Workspace -> Vendor -> Cache)
- [[Open Source Monetization Model B]] -- fully open source + managed service revenue

## Reference Documents (Works)

- [[DUUMBI - PRD]] -- current product strategy, architecture, and long-term vision
- [[DUUMBI - Product Roadmap 2026-05]] -- current execution roadmap
- [[DUUMBI Roadmap Map]] -- roadmap hub
- [[DUUMBI - MVP Specification]] -- original Phase 0-3 specification
- [[DUUMBI - Glossary]] -- canonical term definitions
- [[DUUMBI - Post-MVP Roadmap]] -- historical/supporting business plan and monetization strategy
- [[DUUMBI - Graph Repository Architecture]] -- detailed registry and namespace design

## For AI Agents

If you are an AI agent working on DUUMBI:
1. Read [[DUUMBI - Glossary]] first for canonical term definitions.
2. Read [[DUUMBI - PRD]] for current product strategy and architecture.
3. Read [[DUUMBI - Product Roadmap 2026-05]] and [[DUUMBI Roadmap Map]] for active milestone state.
4. Treat GitHub issues as the source of truth for completion state.
5. Every graph mutation must reach the [[Semantic Fixed Point]] before compilation.
6. Follow the JSON-LD schema at `.duumbi/schema/core.schema.json`.
