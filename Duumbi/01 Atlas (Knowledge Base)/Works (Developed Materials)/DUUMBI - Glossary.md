---
tags:
  - project/duumbi
  - doc/glossary
status: active
created: 2026-02-08
updated: 2026-05-07
---

# DUUMBI - Glossary

## DUUMBI

An intent-driven development system that connects product intent, semantic graph structure, executable behavior, runtime feedback, and agentic development workflows.

## Intent

A human-stated goal or desired behavior. In DUUMBI, intent should be clarified into explicit defaults, inputs, outputs, visible states, edge cases, invariants, and verification steps before implementation.

## Semantic Graph

A structured representation of program meaning using nodes, edges, identifiers, types, and metadata. DUUMBI uses graph structure so behavior can be inspected, transformed, compiled, and linked to knowledge.

## JSON-LD

JSON for linked data. DUUMBI uses it as a graph-friendly, tool-friendly representation that can remain compatible with common JSON tooling while carrying semantic identity.

## Node ID

A stable identifier for a semantic graph node. Node IDs should let humans, agents, tests, telemetry, and UI surfaces refer to the same concept.

## Compilation Pipeline

The transformation path from validated graph representation to executable output. The pipeline should be inspectable and testable at each boundary.

## Cranelift

A Rust compiler backend used for generating executable machine code from an intermediate representation. In DUUMBI, it is relevant because it supports an embeddable graph-to-code workflow.

## Agentic Development

A development model where humans define intent, constraints, and acceptance criteria while agents perform implementation, inspection, review, and evidence gathering.

## Warp Oz

Warp's orchestration layer for running and managing agents locally, in the cloud, across integrations, and through auditable agent sessions.

## Codex

OpenAI's coding agent used in DUUMBI for local repo work, vault maintenance, documentation updates, skill authoring, PR review handling, and structured implementation support.

## Slack Capture

The use of Slack as a fast mobile surface for ideas, issues, approvals, and run follow-up. Slack is not durable storage; outcomes must move to GitHub or Obsidian.

## GitHub Project

The execution source of truth for current work state, issue state, PR state, CI state, prioritization, and delivery coordination.

## Obsidian Atlas

The active knowledge-base area of the vault. It contains Dots, Maps, and Works that describe stable DUUMBI knowledge.

## Dot

A short atomic knowledge note with a summary, why it matters, DUUMBI usage, sources, and related links.

## Map

A navigation and synthesis note that connects related Dots and Works around a topic.

## Work

A synthesized document built from multiple Dots and Maps. DUUMBI keeps one active PRD as the primary product Work.

## AGENTS.md

A repository instruction file read by Codex before work begins. It defines project-local agent expectations such as commands, review norms, constraints, and style.

## Agent Skill

A reusable operational playbook for a task. In Codex, a skill is a `SKILL.md` file with instructions, and optionally scripts, references, or assets.

## MCP Resource

A Model Context Protocol item exposed by a server to provide context such as files, schemas, application data, or source material.

## MCP Prompt

A Model Context Protocol prompt template exposed by a server so clients can invoke structured instructions with arguments.

## Structured Agent Review

A review artifact produced by an agent that records findings, severity, evidence, tests, risks, and open questions in a consistent format before human review.

## Knowledge Packaging

The process of selecting a small, coherent source set from the vault for a custom GPT, NotebookLM notebook, or agent context bundle.

## Related

- [[DUUMBI - PRD]]
- [[DUUMBI Core Concepts Map]]
- [[DUUMBI Technical Architecture Map]]
- [[DUUMBI Agentic Development Map]]
