---
tags:
  - project/duumbi
  - concept/agent-instructions
status: active
created: 2026-05-07
updated: 2026-05-07
---

# AGENTS.md as Agent Contract

## Summary

`AGENTS.md` is the repository-local contract that tells Codex how to behave in a codebase. It should contain durable commands, constraints, style expectations, safety rules, and validation requirements.

## Why it matters

Agents need stable local instructions at the point of work. A vault note can explain the operating model, but the source repo must carry the instructions that directly govern code changes.

## DUUMBI usage

- Keep repo-specific build/test/review rules in `hgahub/duumbi/AGENTS.md`.
- Keep knowledge-base rules in this vault and its checked-in skills.
- Update `AGENTS.md` when the code workflow changes.
- Avoid stale references to older agent instruction filenames.

## Sources

- [OpenAI Codex AGENTS.md docs](https://developers.openai.com/codex/guides/agents-md)
- [AGENTS.md project site](https://agents.md/)

## Related

- [[Agent Skills as Operational Playbooks]]
- [[Spec-First Agentic Development]]
- [[Warp Oz and Codex Development Toolchain]]
- [[DUUMBI Agentic Development Map]]
