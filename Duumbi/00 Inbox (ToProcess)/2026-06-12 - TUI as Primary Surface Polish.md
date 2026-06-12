---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M0
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# TUI as Primary Surface Polish

## Context

The TUI already exists (`src/cli/repl.rs`, ~2,460 lines, ratatui, Query/Agent modes, session persistence in `.duumbi/session/`, `/resume` command). Strategy says: lead with the read-only `query` surface. The TUI is the first surface to be released publicly (M0), ahead of Desktop/Cloud/Mobile.

## Goal

The TUI is good enough to be the face of the v0.4.0-preview launch: query-first, discoverable, stable, and demonstrably session-persistent.

## Subtasks

1. First-run experience: launching `duumbi` in an empty directory should offer guided `init` and a sample workspace, not an error.
2. Query mode as default with visible mode indicator; Agent (write) mode behind explicit switch + approval gates (already the design — verify and document).
3. Command discoverability: `/help` overlay, slash-command palette, keybinding cheatsheet.
4. Session UX: `/resume` listing with timestamps and summaries; verify archive/restore round-trips; document the session contract (this is the seed of "continue session anywhere" — see [[2026-06-12 - Session Kernel and Event Ledger]]).
5. Robustness pass: terminal resize, narrow terminals, no-color terminals, Windows Terminal.
6. Demo asset: a recorded TUI walkthrough (asciinema/VHS) for duumbi.dev and the launch post.

## Review-Derived Follow-Ups

- [[2026-06-12 - TUI Redraw Stability and Modal Cleanup]]
- [[2026-06-12 - TUI Error Diagnostics Stay In-App]]
- [[2026-06-12 - TUI Provider Setup and Credential Discovery UX]]
- [[2026-06-12 - TUI Progress and Session Evidence UX]]
- [[2026-06-12 - TUI Narrow Terminal and Slash Menu Polish]]

## Acceptance criteria

- A first-time user completes: launch TUI → guided init → query "what exists?" → one approved AI mutation → build/run, without reading external docs.
- Session survives kill/restart and `/resume` restores conversational context.
- Works on macOS Terminal, Linux, and Windows Terminal.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Release v0.4.0-preview TUI-first]]
- [[DUUMBI - Service and Research Direction]] (query-first positioning)
