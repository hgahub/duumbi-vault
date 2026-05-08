---
tags:
  - project/duumbi
  - doc/product-requirements
status: active
created: 2026-02-08
updated: 2026-05-08
related_maps:
  - "[[DUUMBI Core Concepts Map]]"
  - "[[DUUMBI Technical Architecture Map]]"
  - "[[DUUMBI Agentic Development Map]]"
---
# DUUMBI - PRD

## Executive Summary

DUUMBI is an intent-driven development system where product intent, semantic structure, executable code, runtime feedback, and agent activity are connected through a graph-oriented workflow. The product should help developers and business stakeholders understand what exists, why it exists, how it behaves, and how it should safely evolve.

The development model is agentic by design: humans define intent, constraints, and acceptance criteria; agents implement, inspect, review, and produce structured evidence; humans verify outcomes and make product or architectural decisions.

As a service, DUUMBI should also answer read-only questions over the same artifacts before mutation begins: what exists, why it exists, where behavior lives, what depends on it, what evidence proves it, and what risk a change carries.

## Product Thesis

Software work breaks down when intent, implementation, execution state, and knowledge drift apart. DUUMBI should reduce that drift by treating intent and structure as first-class artifacts that can be compiled, inspected, modified, reviewed, and connected to durable knowledge.

The product wins when a user can answer:

- what the system is supposed to do
- why the behavior exists
- where the behavior is represented
- what depends on it
- what agent or human changed it
- what evidence proves it works
- what risk a proposed change carries
- what knowledge must be updated after the change

## Users

- **Developer:** builds and changes DUUMBI behavior using the source repo, Codex, Warp Oz, tests, and PR review.
- **Product/business decision maker:** asks what works, why it matters, what trade-offs exist, and what should be prioritized.
- **Question asker:** uses DUUMBI in read-only mode to locate behavior, dependencies, evidence, and change risk before asking an agent to mutate anything.
- **Agent operator:** turns issues, specs, and Slack captures into agent runs with reviewable evidence.
- **Knowledge maintainer:** converts raw input into source-backed Dots, Maps, Works, and agent skills.
- **Evaluator:** checks whether implementation evidence matches product intent, architectural constraints, and user-facing behavior.

## Core Workflows

- Capture a user intent or problem and classify it as execution work or durable knowledge.
- Ask read-only questions over source, graph, intents, sessions, knowledge, and evidence before choosing a write-capable workflow.
- Convert ambiguous intent into a concise product/spec artifact with defaults, states, edge cases, and verification criteria.
- Produce a technical plan that identifies affected areas, risks, tests, and review evidence.
- Use Codex for local/vault/repo work and Warp Oz for cloud, parallel, scheduled, or long-running work.
- Require structured agent review before human review.
- Use GitHub Project, issues, PRs, and CI as the execution state.
- Sync only stable product, architecture, workflow, and skill knowledge into Obsidian.

## Service Surface

DUUMBI should expose `query` as a first-class read-only service surface in CLI, Studio, README, and public docs. The answer contract should label claims, cite source artifacts, identify graph nodes or source symbols when available, show evidence, summarize dependency impact, and identify change risk.

The service should start with conservative answers:

- what exists in the graph, source, registry, docs, tests, or runtime evidence
- why it exists according to intent, PRD, roadmap, issue, archive, or commit evidence
- where behavior lives in graph nodes, modules, generated code, tests, docs, and traces
- what depends on it through graph edges, module imports, registry packages, and source references
- what proves it through tests, CI, benchmark output, archive evidence, telemetry, or review artifacts
- what risk a change carries through affected graph regions, ownership/type boundaries, missing tests, and human-review checkpoints

## Architecture Principles

- **Intent-first:** natural-language intent should become explicit product behavior and graph changes before implementation begins.
- **Queryable first:** users should be able to inspect existing behavior, evidence, dependencies, and risk before asking DUUMBI or an agent to change the system.
- **Graph-centered:** semantic nodes, edges, identifiers, and metadata should be inspectable and linkable.
- **Compiled where useful:** executable output should be generated from validated structure rather than hidden transformation steps.
- **Evidence-oriented:** every agent run should produce reviewable artifacts: summary, diff, tests, risks, and follow-up items.
- **Human-verifiable:** the system should make product behavior easy to inspect, not just easy to generate.
- **Knowledge-backed:** agent context should come from concise, source-backed notes rather than stale long documents.
- **Tool-agnostic core:** Warp Oz, Codex, Slack, GitHub, and Obsidian are workflow tools; DUUMBI's durable model should not depend on one vendor surface.

## Agent Strategy

DUUMBI follows the Warp/Oz-style operating model: people shape the work and verify outcomes while agents perform implementation-heavy, review-heavy, and synthesis-heavy tasks with explicit rules and evidence.

- **Warp Oz:** preferred for orchestration, cloud execution, parallel work, scheduled automation, Slack-triggered work, session visibility, and audit trails.
- **Codex:** preferred for local source inspection, code changes, vault maintenance, skill authoring, PR review handling, and structured documentation updates.
- **Slack:** mobile capture, status, approvals, and agent follow-up only; durable conclusions move to GitHub or Obsidian.
- **GitHub Project:** execution source of truth for issue state, PR state, CI state, and work sequencing.
- **Obsidian:** durable knowledge substrate for concepts, architecture, operating model, source-backed Dots, and packaging for GPT/NotebookLM.

## Knowledge Strategy

The vault should train humans and agents without flooding them with execution history.

- Keep the active source set small: PRD, Service and Research Direction, Glossary, Core Concepts Map, Technical Architecture Map, Agentic Development Map, and the relevant Dots.
- Break complex concepts into Dots with explicit sources and related links.
- Use Maps as navigation and synthesis, not as status logs.
- Store raw captures in `00 Inbox (ToProcess)/` until classified.
- Preserve old delivery documents in the archive for history, not active guidance.
- Package the active source set for custom GPTs and NotebookLM; exclude archive material unless a specific historical question requires it.

## Non-Goals

- Obsidian does not track current execution status.
- Slack is not a durable decision store.
- Agent output is not accepted without tests, review, or explicit human verification.
- The vault does not duplicate source-code documentation that belongs in the DUUMBI repo.
- Long historical planning documents should not be recreated as active guidance.

## Success Criteria

- A new developer or agent can locate execution state, architecture context, workflow rules, and spec requirements within five minutes.
- A user can ask DUUMBI what exists, why it exists, where behavior lives, what proves it, what depends on it, and what risk a change carries.
- Agent runs start from clear specs and produce structured evidence.
- GitHub Project remains the only current execution-state tracker.
- The active NotebookLM/GPT source set is small, coherent, and free of historical delivery noise.
- Dots are concise, source-backed, and link into Maps.
- Slack captures are processed into GitHub items, Inbox notes, or Atlas notes without becoming orphaned decisions.

## Related

- [[DUUMBI - Glossary]]
- [[DUUMBI - Service and Research Direction]]
- [[DUUMBI Core Concepts Map]]
- [[DUUMBI Technical Architecture Map]]
- [[DUUMBI Agentic Development Map]]
- [[Spec-First Agentic Development]]
- [[Warp Oz and Codex Development Toolchain]]
- [[GitHub Project as Execution Source of Truth]]
- [[Obsidian Vault as Agent Knowledge Substrate]]
