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

# Agent Substrate: MCP as First-Class Interface

## Context

*Proposed addition (Claude, 2026-06-12), from the "how would I use this system" exercise.* As an AI agent, generating raw source text is where my failure modes live: hallucinated APIs, type errors discovered late, silent behavioral drift, no memory of what I already built. DUUMBI removes whole hallucination classes by construction — the mutation target is a typed, ownership-checked, schema-validated graph with deterministic compiler feedback and machine-readable errors, plus reuse lookup so I don't rebuild what exists. This is exactly the [[DUUMBI - Service and Research Direction]] positioning ("semantic execution substrate for coding agents"), and the MCP server (rmcp, 10 tools) already exists. What's missing: external agents are not yet first-class, evaluated users.

## Goal

Any off-the-shelf coding agent (Claude Code, Codex, etc.) can drive the full DUUMBI loop — init, query, intent, patch, build, run, evidence — through MCP alone, and this is a tested, documented, marketed product path, not a side feature.

## Subtasks

1. MCP tool surface audit: map the 10 existing tools against the full workflow; close gaps so no step requires a human at a terminal (init/workspace management, intent lifecycle, patch approval flow, build/run with captured output, evidence/query access — including the open MODE-010 conversational query tool from the modes spec).
2. Machine-readable error contract: every failure (parse, type, ownership, build, runtime) returns structured data an agent can act on — error code, offending node ids, suggested repair categories. No prose-only errors.
3. Approval gate design for agent callers: how a human approves agent-initiated mutations when the agent is the client (session ledger events + TUI/Studio approval surface; later mobile — [[2026-06-12 - Mobile App and Supervision Surface]]).
4. Agent onboarding docs: an AGENTS.md-grade "how to drive DUUMBI as a tool" guide, published on docs.duumbi.dev, plus a Claude Code skill/plugin package.
5. Benchmark: external agent builds the flagship example (HTTP + SQLite + JSON) end-to-end via MCP only; measure success rate, turns, tokens; compare against the same agent writing Rust directly — quantify the hallucination-class reduction and feed the token numbers into [[2026-06-12 - Token Economics Benchmark]].
6. Publish as case study (Phase 14 GTM): "an AI agent's view of DUUMBI" — why a substrate beats raw text generation.

## Acceptance criteria

- An unmodified Claude Code session completes intent → build → run on the flagship example using only DUUMBI MCP tools.
- Every error path returns structured, actionable data (verified by tests).
- Agent guide live on docs.duumbi.dev; benchmark numbers published.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Intent at Scale Multi-Module and BDD]]
- [[2026-06-12 - Determinism Program for AI Development]]
