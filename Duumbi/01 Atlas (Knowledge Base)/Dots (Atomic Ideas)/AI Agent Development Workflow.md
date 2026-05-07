---
tags:
  - project/duumbi
  - concept/agent-workflow
status: active
created: 2026-02-08
updated: 2026-05-07
---

# AI Agent Development Workflow

## Summary

DUUMBI's agent workflow is a durable loop: capture, clarify, plan, implement, review, verify, merge, and sync knowledge.

## Why it matters

Agents need structured handoffs and evidence. The workflow should survive tool changes because it is based on roles and artifacts, not a single agent vendor.

## DUUMBI usage

1. Capture work in GitHub or Inbox.
2. Clarify product/spec behavior.
3. Create a technical plan.
4. Run Codex locally or Warp Oz for orchestration-heavy work.
5. Produce structured review artifacts.
6. Human verifies product and architecture fit.
7. CI and PR review decide merge readiness.
8. Update Dots, Maps, skills, or `AGENTS.md` only when durable knowledge changes.

## Sources

- [What Warp’s Open Source Release Tells Us About the Future of Agentic Software Development](https://medium.com/jonathans-musings/what-warps-open-source-release-tells-us-about-the-future-of-agentic-software-development-5d4409726bf1)
- [Warp Oz overview](https://docs.warp.dev/agent-platform)
- [OpenAI Codex skills docs](https://developers.openai.com/codex/skills)

## Related

- [[DUUMBI Agentic Development Map]]
- [[Spec-First Agentic Development]]
- [[Structured Agent Review Artifacts]]
- [[Warp Oz and Codex Development Toolchain]]
