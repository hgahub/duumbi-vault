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
source: "Manual TUI UX review, 2026-06-12"
parent: "[[2026-06-12 - TUI as Primary Surface Polish]]"
---

# TUI Error Diagnostics Stay In-App

## Context

Manual review intentionally corrupted `.duumbi/graph/main.jsonld` and ran `/check` from the TUI. The product produced useful diagnostics, including JSONL, error code, source location, and suggestions. However, the TUI dropped out of the alternate-screen interface and printed raw CLI output with `[Press Enter to return to the REPL]`.

The UI recovered after pressing Enter, but the diagnostic was not preserved as a normal conversation block, making the error easy to miss and hard to review.

## Goal

Build/check/run failures should render inside the TUI as structured, scrollable, persistent diagnostic blocks. The user should see what failed, where, why, suggested next actions, and whether the process is recoverable without leaving the TUI surface.

## Observed Evidence

- Fresh workspace: `/tmp/duumbi-tui-ux.XkZdLF`.
- Graph corruption inserted invalid JSON at line 2.
- `/check` left the alternate-screen UI and printed:
  - JSONL diagnostic with code `INTERNAL`.
  - Human error: invalid JSON expected value at line 2 column 20.
  - Suggestions: ask AI to fix, undo, describe.
  - `[Press Enter to return to the REPL]`.
- After return, the conversation showed `/check` but not the diagnostic output as a normal retained block.

## Subtasks

1. Capture stdout/stderr from `/check`, `/build`, and `/run` inside the REPL command path instead of temporarily leaving the TUI.
2. Render diagnostics as a structured block: status, command, elapsed time, error code, location, message, and suggested actions.
3. Preserve error blocks in session history and `/history`, not only in transient terminal output.
4. Keep machine-readable diagnostic details available for copy/export without showing raw JSONL as the default human view.
5. Add regression tests for parse error, validation error, compilation error, link error, and runtime failure display.

## Acceptance Criteria

- A malformed graph produces an in-app error block and the TUI layout remains intact.
- The user can scroll back to the diagnostic after recovery.
- Suggested next actions are visible and actionable.
- No command path requires `[Press Enter to return to the REPL]` for ordinary errors.

## Links

- [[2026-06-12 - TUI as Primary Surface Polish]]
- [[2026-06-12 - Codegen Trap Discipline and Backend Hardening]]
- [[2026-06-12 - Agent Substrate MCP First-Class]]
