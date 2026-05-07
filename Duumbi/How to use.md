---
tags:
  - project/duumbi
  - tool/obsidian
  - doc/reference
status: active
created: 2026-02-08
updated: 2026-05-07
---

# How to Use This Vault

This vault is DUUMBI's durable knowledge base for product, architecture, workflow, and agent-skill context. It is not the execution tracker. Current work state lives in the DUUMBI GitHub Project and source repository issues/PRs.

## Start Here

Read in this order when onboarding a developer, Codex session, Warp Oz run, custom GPT, or NotebookLM notebook:

1. [[DUUMBI - PRD]] -- product thesis, users, workflows, architecture principles, agent strategy, non-goals, and success criteria.
2. [[DUUMBI - Glossary]] -- shared terms used by product, code, and agents.
3. [[DUUMBI Core Concepts Map]] -- conceptual entrypoint into the atomic notes.
4. [[DUUMBI Technical Architecture Map]] -- durable technical model and active architecture Dots.
5. [[DUUMBI Agentic Development Map]] -- operating model for spec-first agentic development.
6. Repository `AGENTS.md` in `hgahub/duumbi` -- the source-code agent contract for build, test, review, and coding norms.

## Source Of Truth Rules

- **Execution state:** GitHub Project, GitHub issues, PRs, CI, and linked code reviews.
- **Durable knowledge:** this Obsidian vault.
- **Repo-specific agent behavior:** repository `AGENTS.md` and checked-in skills.
- **Mobile capture and approvals:** Slack, with durable outcomes linked back into GitHub or Obsidian.
- **Agent orchestration:** Warp Oz for cloud, parallel, scheduled, and long-running work; Codex for local repo/vault inspection, implementation support, PR review handling, skill authoring, and knowledge-base maintenance.

## Vault Structure

- `00 Inbox (ToProcess)/` -- raw ideas, Slack captures, article links, and unresolved research input.
- `01 Atlas (Knowledge Base)/Dots (Atomic Ideas)/` -- short, source-backed atomic notes.
- `01 Atlas (Knowledge Base)/Maps (Overviews)/` -- navigation hubs that connect Dots and active Works.
- `01 Atlas (Knowledge Base)/Works (Developed Materials)/` -- current synthesized documents. The single active product requirements document is [[DUUMBI - PRD]].
- `02 Resources (Assets and Tools)/` -- source material, assets, and reusable references.
- `05 Archive/` -- preserved historical material only. Do not use archived notes as current guidance unless explicitly labeled historical in the active note that links them.

## Operating Workflow

1. Capture an idea, bug, question, or external source in Slack, GitHub, or `00 Inbox (ToProcess)/`.
2. Classify it:
   - execution item -> GitHub issue or Project item
   - durable knowledge -> Dot, Map, Work, or source note in this vault
3. Clarify product behavior before implementation.
4. Produce a technical plan with validation steps and affected files.
5. Use Warp Oz or Codex to implement, inspect, review, and gather evidence.
6. Require structured agent review artifacts before human review.
7. Human reviews behavior, product fit, and risk.
8. CI and PR review decide merge readiness.
9. Sync only durable learnings back into Obsidian.

## Spec Quality Bar

Before implementation, a spec must make agent behavior unambiguous:

- default behavior
- inputs and outputs
- visible states
- error and empty states
- cancellation behavior
- offline, retry, and race-condition behavior
- accessibility and focus expectations
- invariants that must not change
- verification steps and evidence expected from the agent

## Dot Rules

Dots must stay short and direct. Each active Dot should include:

- `Summary`
- `Why it matters`
- `DUUMBI usage`
- `Sources`
- `Related`

Use primary sources where possible. Prefer official docs, source repos, papers, standards, and direct project evidence. Do not turn Dots into execution logs.
