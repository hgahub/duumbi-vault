---
tags:
  - project/duumbi
  - concept/execution-tracking
status: active
created: 2026-05-07
updated: 2026-05-07
---

# GitHub Project as Execution Source of Truth

## Summary

GitHub Project is the only current execution-state tracker for DUUMBI. Issues, PRs, CI, review threads, and Project fields decide what is active, blocked, done, or ready for review.

## Why it matters

Duplicating execution state in Obsidian creates stale guidance. Agents must inspect GitHub before claiming status or choosing the next implementation task.

## DUUMBI usage

- Create execution items in GitHub, not in long vault plans.
- Link durable decisions and architecture notes from Obsidian when useful.
- Use Project fields and views for prioritization and status.
- Treat Obsidian as context, not delivery state.

## Sources

- [GitHub Projects docs](https://docs.github.com/en/issues/planning-and-tracking-with-projects)
- [Warp open-source announcement](https://www.warp.dev/blog/warp-is-now-open-source)

## Related

- [[Obsidian Vault as Agent Knowledge Substrate]]
- [[Slack as Mobile Capture Surface]]
- [[Structured Agent Review Artifacts]]
- [[DUUMBI Agentic Development Map]]
