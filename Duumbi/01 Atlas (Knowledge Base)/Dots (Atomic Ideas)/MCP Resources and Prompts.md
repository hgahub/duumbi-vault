---
tags:
  - project/duumbi
  - concept/mcp
status: active
created: 2026-05-07
updated: 2026-05-07
---

# MCP Resources and Prompts

## Summary

MCP resources expose contextual data to clients, while MCP prompts expose reusable prompt templates. Together they let agents discover structured context and workflows through a standard protocol.

## Why it matters

DUUMBI agents need reliable ways to access vault notes, repo data, schemas, review templates, and workflow prompts without pasting large context manually.

## DUUMBI usage

- Use MCP resources for files, schemas, graph snapshots, source notes, and knowledge-base bundles.
- Use MCP prompts for repeatable workflows such as spec clarification, review artifact generation, Inbox processing, and architecture analysis.
- Validate prompt inputs and outputs to reduce injection and accidental misuse.
- Prefer small, named resources over large undifferentiated context blobs.

## Sources

- [MCP resources](https://modelcontextprotocol.io/docs/concepts/resources)
- [MCP prompts](https://modelcontextprotocol.io/docs/concepts/prompts)

## Related

- [[Agent Skills as Operational Playbooks]]
- [[Obsidian Vault as Agent Knowledge Substrate]]
- [[GPT and NotebookLM Knowledge Packaging]]
- [[AI Agent Architecture]]
