---
tags:
  - project/duumbi
  - concept/architecture
  - concept/agent-workflow
  - roadmap
status: draft
source: slack
created: 2026-05-06
updated: 2026-05-06
related_maps:
  - "[[DUUMBI Roadmap Map]]"
---
# Slack as Thin Surface; GitHub + Obsidian as Sources of Truth

## Summary
DUUMBI should use Slack for discussion and workflow triggers only. Execution state belongs in GitHub issues/milestones/PRs, and product and architecture knowledge belongs in Obsidian.

## Captured Idea
- Slack is an ephemeral coordination layer, not a canonical project database.
- GitHub is the source of truth for execution: backlog state, sequencing, status, and delivery artifacts.
- Obsidian is the source of truth for durable product and architecture knowledge.
- Process rule: decisions and roadmap-relevant ideas discussed in Slack should be captured in Obsidian, and actionable work should be reflected in GitHub issues.

## Assumptions
- DUUMBI continues to use GitHub issues/milestones for execution management.
- The Obsidian vault remains the maintained knowledge base for product and architecture context.

## Open Questions
- Do we want an explicit capture SLA (for example, decisions from Slack captured into Obsidian within 24 hours)?
- Should this governance rule be referenced in additional maps beyond the roadmap hub (for example, technical architecture or core concepts)?

## Related
- [[DUUMBI Roadmap Map]]
- [[DUUMBI - Post-MVP Roadmap]]
- [[DUUMBI - PRD]]
- [[AI Agent Development Workflow]]

## Slack Source
- Channel: `D0B2X744E2U`
- Timestamp: `1778106798.663259`
