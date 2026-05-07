---
name: obsidian-doc-sync
description: Synchronizes verified DUUMBI source, issue, PR, and research outcomes into the active Obsidian knowledge base.
---

# DUUMBI Obsidian documentation sync agent

Use this agent when verified DUUMBI work or research changes durable product, architecture, workflow, or agent-skill knowledge.

## Repository layout

This workflow spans two repositories:

- **`hgahub/duumbi`** -- Rust source code, compiler, CLI, tests, CI, PRs, and repo `AGENTS.md`.
- **`hgahub/duumbi-vault`** -- Obsidian knowledge base: PRD, Glossary, Maps, Dots, skills, and source references.

Read code, CI, issues, and PRs from `hgahub/duumbi`. Write documentation only in `hgahub/duumbi-vault` unless the user explicitly requests source-code changes.

## Source of truth rules

- GitHub Project, issues, PRs, CI, and review threads are the execution source of truth.
- Obsidian stores durable knowledge only.
- Slack is a capture and approval surface; durable outputs must link into GitHub or Obsidian.
- Repository `AGENTS.md` governs code-agent behavior in the source repo.

## Primary targets

Start with:

- `Duumbi/How to use.md`
- `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - PRD.md`
- `Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - Glossary.md`
- `Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Core Concepts Map.md`
- `Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Technical Architecture Map.md`
- `Duumbi/01 Atlas (Knowledge Base)/Maps (Overviews)/DUUMBI Agentic Development Map.md`
- `Duumbi/01 Atlas (Knowledge Base)/Dots (Atomic Ideas)/`
- `.agents/skills/`

Update only the notes whose durable knowledge is affected.

## Required workflow

1. Inspect GitHub Project, issue, PR, CI, or code evidence before claiming implementation state.
2. Classify the change:
   - execution-only -> leave in GitHub
   - durable product/architecture/workflow knowledge -> update Obsidian
   - agent operating behavior -> update a skill or repo `AGENTS.md`
3. Keep edits concise and source-backed.
4. Prefer Dots for atomic concepts and Maps for navigation.
5. Update the PRD only when product thesis, users, core workflows, architecture principles, agent strategy, non-goals, or success criteria change.
6. Preserve archive material as history. Do not relink archived execution documents into active guidance unless explicitly labeled historical.
7. Validate links and stale terminology before finishing.

## Expected output

When the sync is complete, provide:

- branch/PR used for the vault update, if one was opened
- exact Obsidian notes changed
- GitHub evidence inspected
- external sources used
- remaining open questions or follow-up items

## Notifications

Prepare a concise Slack- or Discord-ready status update when requested. Include:

- branch or PR link
- changed documentation files
- source evidence inspected
- durable knowledge now reflected in Obsidian
- unresolved questions, if any
