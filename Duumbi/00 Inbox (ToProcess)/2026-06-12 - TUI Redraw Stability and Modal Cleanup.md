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

# TUI Redraw Stability and Modal Cleanup

## Context

Manual review of `target/debug/duumbi` in a fresh workspace found redraw corruption in normal TUI use. The provider manager modal opened over existing status output without clearing the underlying left gutter, leaving stale fragments beside the panel. Closing the modal with `Esc` also left large portions of stale status and prompt text in the main pane before exit.

This fits the M0 TUI release-polish track: the UI must not visually fall apart during ordinary setup and recovery flows.

## Goal

Modal open/close, panel transitions, command output, and normal exit always repaint the affected regions cleanly. The terminal should never show stale fragments from a previous panel, prompt, or conversation block.

## Observed Evidence

- Fresh workspace: `/tmp/duumbi-tui-ux.XkZdLF`.
- Command: `target/debug/duumbi`.
- Opened `/provider`; stale fragments from the underlying status pane appeared in the left margin beside the modal.
- Pressed `Esc`; the main pane still contained mixed provider/status/prompt fragments.
- Exiting with `Ctrl-D` left a visibly corrupted final screen before terminal restoration.

## Subtasks

1. Audit ratatui layout clearing for modal overlays, especially provider manager and status/history panes.
2. Add render tests that verify modal rectangles clear their full background, including surrounding gutters and borders.
3. Add an integration smoke path for `/status` -> `/provider` -> `Esc` -> `Ctrl-D` and assert no stale provider/status text remains in the rendered buffer.
4. Ensure alternate-screen teardown restores the terminal cleanly after corrupted-render paths and normal exit.
5. Repeat the pass at 80x24 and a narrow terminal size such as 60x18.

## Acceptance Criteria

- Provider panel open and close leaves no stale underlying text.
- Status, provider, help, and resume panels can be opened and closed repeatedly without visual residue.
- Automated tests cover modal background clearing and at least one narrow terminal size.

## Links

- [[2026-06-12 - TUI as Primary Surface Polish]]
- [[2026-06-12 - Release v0.4.0-preview TUI-first]]
