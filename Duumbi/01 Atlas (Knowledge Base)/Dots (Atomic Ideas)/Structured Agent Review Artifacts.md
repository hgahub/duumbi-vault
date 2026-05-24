---
tags:
  - project/duumbi
  - concept/agent-review
status: active
created: 2026-05-07
updated: 2026-05-07
---

# Structured Agent Review Artifacts

## Summary

A structured agent review artifact is a consistent evidence package produced before human review. It records what changed, how it was verified, what risks remain, and which findings need attention.

## Why it matters

Human review should evaluate behavior and trade-offs, not reconstruct the agent's work from a transcript. Structured review makes agent output auditable and comparable across runs.

## DUUMBI usage

Every significant Codex implementation should produce:

- summary of change
- affected files or modules
- tests and commands run
- evidence links or logs
- findings grouped by severity
- risks, assumptions, and open questions
- explicit "ready for human review" or "blocked" status

## Sources

- [Anthropic: Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)

## Related

- [[Spec-First Agentic Development]]
- [[Codex Development Toolchain]]
- [[GitHub Project as Execution Source of Truth]]
- [[AI Agent Development Workflow]]
