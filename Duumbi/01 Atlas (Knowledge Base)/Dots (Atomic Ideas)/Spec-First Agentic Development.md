---
tags:
  - project/duumbi
  - concept/agent-workflow
status: active
created: 2026-05-07
updated: 2026-05-07
---

# Spec-First Agentic Development

## Summary

Spec-first agentic development means agents should not start implementation from vague intent. The spec must define behavior, states, constraints, invariants, and verification clearly enough that an agent can implement and review against it.

## Why it matters

Agents are effective at implementation and iteration when success criteria are concrete. Ambiguous specs create confident but unverifiable output.

## DUUMBI usage

A DUUMBI spec should include defaults, inputs, outputs, visible states, empty states, error states, cancellation, offline/retry behavior, race conditions, accessibility/focus rules, invariants, test expectations, and evidence artifacts.

## Sources

- Local article: `/Users/heizergabor/Downloads/What Warp’s Open Source Release Tells Us About the Future of Agentic Software Development _ by Jonathan Fulton _ Jonathan’s Musings _ May, 2026 _ Medium.html`
- [Warp open-source announcement](https://www.warp.dev/blog/warp-is-now-open-source)
- [Anthropic: Building effective agents](https://www.anthropic.com/engineering/building-effective-agents)

## Related

- [[DUUMBI Agentic Development Map]]
- [[Structured Agent Review Artifacts]]
- [[AGENTS.md as Agent Contract]]
- [[Agent Skills as Operational Playbooks]]
