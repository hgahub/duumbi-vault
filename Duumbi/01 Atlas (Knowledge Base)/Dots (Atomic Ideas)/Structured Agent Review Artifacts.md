---
tags:
  - project/duumbi
  - concept/agent-review
status: active
created: 2026-05-07
updated: 2026-06-03
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
- AI review evidence: Codex self-review, Copilot review state, optional CodeRabbit comments, and Greptile status when applicable
- findings grouped by severity
- risks, assumptions, and open questions
- explicit "ready for human review" or "blocked" status

For DUUMBI Stage 11, Greptile status must be explicit: `not needed`,
`recommended for human decision`, `manually requested`, or `completed`. Missing
Greptile evidence is not a default blocker because Greptile is manual-only and
reserved for high-risk implementation PRs.

## Sources

- [Anthropic: Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)
- [[AI Code Review Service Policy]]

## Related

- [[Spec-First Agentic Development]]
- [[Codex Development Toolchain]]
- [[GitHub Project as Execution Source of Truth]]
- [[AI Agent Development Workflow]]
- [[AI Code Review Service Policy]]
