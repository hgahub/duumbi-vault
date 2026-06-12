---
tags:
  - project/duumbi
  - map/agentic-development
status: active
created: 2026-05-07
updated: 2026-06-03
---

# DUUMBI Agentic Development Map

This is the hub for DUUMBI's agentic development operating model. It follows the Codex-centered pattern: humans define intent and verify outcomes; agents implement, review, and produce structured evidence.

## Durable Workflow

1. Capture idea, bug, question, or source in Slack, GitHub, or `00 Inbox (ToProcess)/`.
2. Ask read-only questions first when context is unclear: what exists, why it exists, where behavior lives, what depends on it, what proves it, and what risk a change carries.
3. Route execution work to GitHub Project and durable knowledge to Obsidian.
4. Clarify product/spec behavior until defaults, states, errors, cancellation, accessibility, invariants, and verification are explicit.
5. Create a technical plan with affected files, risks, test strategy, and evidence expectations.
6. Use Codex App for human-controlled delivery, Codex Cloud for approved cloud or scheduled work, and Codex CLI for local repo/vault work.
7. Require structured agent review before human review.
8. Use CI, PR review, and human verification before merge.
9. Sync stable learnings back into Dots, Maps, skills, or `AGENTS.md`.

## Tool Roles

- [[Codex Development Toolchain]] -- where each tool fits.
- [[Slack as Mobile Capture Surface]] -- quick capture, approvals, and agent follow-up.
- [[GitHub Project as Execution Source of Truth]] -- issue/PR state and execution workflow.
- [[Obsidian Vault as Agent Knowledge Substrate]] -- durable product and architecture memory.
- [[DUUMBI Repository Map]] -- repository ownership before routing work.
- [[AGENTS.md as Agent Contract]] -- repository-local agent expectations.
- [[Agent Skills as Operational Playbooks]] -- repeatable workflows for agents.
- [[MCP Resources and Prompts]] -- structured context and prompt surfaces.

## Spec And Review

- [[DUUMBI - Service and Research Direction]]
- [[DUUMBI - Agentic Development Runbook]]
- [[Spec-First Agentic Development]]
- [[Structured Agent Review Artifacts]]
- [[AI Code Review Service Policy]]
- [[AI Agent Development Workflow]]
- [[AI Agent Architecture]]

## Knowledge Packaging

- [[Inbox-to-Atlas Processing Workflow]]
- [[Visual Documentation in Obsidian]]
- [[GPT and NotebookLM Knowledge Packaging]]
- [[DUUMBI Core Concepts Map]]
- [[DUUMBI Technical Architecture Map]]

## Primary Sources

- [OpenAI Codex AGENTS.md docs](https://developers.openai.com/codex/guides/agents-md)
- [OpenAI Codex skills docs](https://developers.openai.com/codex/skills)
