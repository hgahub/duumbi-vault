---
tags:
  - project/duumbi
  - concept/agent-skills
status: active
created: 2026-05-07
updated: 2026-05-07
---

# Agent Skills as Operational Playbooks

## Summary

Agent skills are reusable playbooks that package task-specific instructions, references, and optional scripts so agents can perform repeated workflows consistently.

## Why it matters

High-value agent work depends on repeatable process. Skills reduce prompt drift, keep workflows discoverable, and let agents load detailed context only when relevant.

## DUUMBI usage

- Create skills for Inbox processing, PR review handling, source-backed Dot creation, release synthesis, and architecture review.
- Keep skills concise at the trigger level and detailed in `SKILL.md`.
- Include references or scripts only when they materially improve reliability.
- Update skills when review evidence shows the workflow needs correction.

## Sources

- [OpenAI Codex skills docs](https://developers.openai.com/codex/skills)
- [Warp skills as agents docs](https://docs.warp.dev/agent-platform/cloud-agents/skills-as-agents/)

## Related

- [[AGENTS.md as Agent Contract]]
- [[Inbox-to-Atlas Processing Workflow]]
- [[Structured Agent Review Artifacts]]
- [[MCP Resources and Prompts]]
