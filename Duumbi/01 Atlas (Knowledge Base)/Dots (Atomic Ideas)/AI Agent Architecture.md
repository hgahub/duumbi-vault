---
tags:
  - project/duumbi
  - concept/agent-architecture
status: active
created: 2026-02-08
updated: 2026-05-07
---

# AI Agent Architecture

## Summary

DUUMBI's agent architecture should connect intent, graph context, source-code context, tool execution, review evidence, and human checkpoints.

## Why it matters

Agents are useful when their context, tools, and stopping conditions are explicit. They become risky when they operate from stale memory or vague instructions.

## DUUMBI usage

- Use `AGENTS.md` for repo-local instructions.
- Use skills for repeatable workflows.
- Use MCP resources and prompts for structured context.
- Use Oz or Codex runs to produce evidence, not just code.
- Keep human approval at product, architecture, and merge checkpoints.

## Sources

- [Anthropic: Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)
- [Warp Oz overview](https://docs.warp.dev/agent-platform)
- [OpenAI Codex skills docs](https://developers.openai.com/codex/skills)
- [MCP resources](https://modelcontextprotocol.io/docs/concepts/resources)

## Related

- [[Warp Oz and Codex Development Toolchain]]
- [[AGENTS.md as Agent Contract]]
- [[Agent Skills as Operational Playbooks]]
- [[MCP Resources and Prompts]]
