---
tags:
  - project/duumbi
  - concept/agent-workflow
status: active
created: 2026-05-07
updated: 2026-05-07
---

# Warp Oz and Codex Development Toolchain

## Summary

Warp Oz and Codex are complementary development agents. Oz is the orchestration layer for cloud, parallel, scheduled, integration-triggered, and auditable agent work. Codex is the local/vault/repo agent for inspection, implementation support, documentation, skill authoring, and PR review handling.

## Why it matters

Agentic development fails when tool responsibility is ambiguous. DUUMBI needs a clear split so agents know where to run, where evidence lives, and where durable knowledge is updated.

## DUUMBI usage

- Use Warp Oz for long-running, parallel, cloud, scheduled, Slack-triggered, or team-visible runs.
- Use Codex for local code changes, vault edits, source inspection, review feedback, and skill maintenance.
- Store execution state in GitHub Project and PRs.
- Sync durable lessons into Obsidian after verification.

## Sources

- Local article: `/Users/heizergabor/Downloads/What Warp’s Open Source Release Tells Us About the Future of Agentic Software Development _ by Jonathan Fulton _ Jonathan’s Musings _ May, 2026 _ Medium.html`
- [Warp open-source announcement](https://www.warp.dev/blog/warp-is-now-open-source)
- [Warp Oz overview](https://docs.warp.dev/agent-platform)
- [Warp Oz platform docs](https://docs.warp.dev/agent-platform/cloud-agents/platform)
- [OpenAI Codex AGENTS.md docs](https://developers.openai.com/codex/guides/agents-md)

## Related

- [[DUUMBI Agentic Development Map]]
- [[Spec-First Agentic Development]]
- [[Structured Agent Review Artifacts]]
- [[GitHub Project as Execution Source of Truth]]
