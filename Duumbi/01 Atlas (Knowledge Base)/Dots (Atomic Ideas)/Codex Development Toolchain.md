---
tags:
  - project/duumbi
  - concept/agent-workflow
status: active
created: 2026-05-07
updated: 2026-06-03
---

# Codex Development Toolchain

## Summary

DUUMBI uses Codex as the agent execution family. Codex App is the primary human-controlled delivery environment, Codex Cloud handles scheduled, cloud, parallel, or long-running work when explicitly configured, and Codex CLI supports local terminal-driven inspection and execution.

## Why it matters

Agentic development fails when tool responsibility is ambiguous. DUUMBI needs a clear split so agents know where to run, where evidence lives, and where durable knowledge is updated.

## DUUMBI usage

- Use Codex App for mobile-supervised delivery autopilot, high-cost work, review handling, and developer-controlled implementation.
- Use Codex Cloud for scheduled, cloud, parallel, or long-running runs only through approved dispatch contracts.
- Use Codex CLI for local repo/vault inspection, focused edits, validation, and PR review handling.
- Use Codex self-review as the mandatory contextual review layer before PR readiness, spec AI gates, and Stage 11 recommendations.
- Store execution state in GitHub Project, issues, PRs, CI, and review threads.
- Sync durable lessons into Obsidian only after verification.

## Sources

- [OpenAI Codex AGENTS.md docs](https://developers.openai.com/codex/guides/agents-md)
- [[DUUMBI - Agentic Development Runbook]]

## Related

- [[DUUMBI Agentic Development Map]]
- [[Spec-First Agentic Development]]
- [[Structured Agent Review Artifacts]]
- [[AI Code Review Service Policy]]
- [[GitHub Project as Execution Source of Truth]]
