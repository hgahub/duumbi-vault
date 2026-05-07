---
tags:
  - project/duumbi
  - concept/tui
status: active
created: 2026-02-08
updated: 2026-05-07
---

# DUUMBI - TUI - Provider

## Summary

The TUI provider pattern separates terminal interaction concerns from product behavior and graph operations.

## Why it matters

A terminal UI can become hard to test if state, rendering, input handling, and domain logic are mixed. A provider boundary helps agents modify UI behavior without breaking graph or compiler behavior.

## DUUMBI usage

- Keep rendering, input events, domain commands, and graph mutations separable.
- Specify keyboard/focus behavior before implementation.
- Require UI-state evidence for interactive changes.
- Use Codex locally for inspection and targeted changes; use structured review for regressions.

## Sources

- [Ratatui](https://ratatui.rs/)
- [Ratatui API docs](https://docs.rs/ratatui/latest/ratatui/)

## Related

- [[Spec-First Agentic Development]]
- [[Structured Agent Review Artifacts]]
- [[DUUMBI Technical Architecture Map]]
