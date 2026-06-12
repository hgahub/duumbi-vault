---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/medium
  - duumbi/importance/high
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M0
source: "Manual TUI UX review, 2026-06-12"
parent: "[[2026-06-12 - TUI as Primary Surface Polish]]"
---

# TUI Narrow Terminal and Slash Menu Polish

## Context

Manual review launched the TUI in a narrow terminal (`stty cols 60 rows 18`). The UI remained usable, but several elements degraded in ways that make the first-release surface feel unfinished: footer text clipped mid-word, command descriptions truncated without clear indication, the prompt lost its bordered input box, and the bottom `cwd` value was clipped aggressively.

This is a robustness and polish subtask under M0, not a separate product initiative.

## Goal

The TUI should degrade intentionally on small terminals: compact layout, predictable truncation, no misleading half-controls, and a clear minimum-size message when the screen is too small for the full interface.

## Observed Evidence

- Test size: 60 columns x 18 rows.
- First screen remained functional, but the top help text lost the `Ctrl-D` exit hint.
- Slash-menu footer showed truncated text such as `Esc clo`.
- Command descriptions cut off mid-word, e.g. registry/resume descriptions.
- Prompt rendered as a bare input line instead of the normal bordered input box.
- Footer path shortened to an unclear `/pri….Xk` fragment.

## Subtasks

1. Define minimum supported terminal dimensions for the full TUI.
2. Add compact layout rules for narrow/short terminals: shorter header, condensed footer, single-line status, and no half-rendered controls.
3. Use consistent ellipsis/truncation helpers for descriptions, footer labels, cwd, and hints.
4. Add render tests at 60x18 and one smaller unsupported size.
5. If below minimum size, show a clear "terminal too small" message with the required dimensions.

## Acceptance Criteria

- At 60x18, the UI remains readable and controls do not truncate into misleading partial words.
- Below the supported minimum size, the user gets a clear size requirement instead of a broken layout.
- Slash-menu filtering and `/resume` remain usable in compact mode.

## Links

- [[2026-06-12 - TUI as Primary Surface Polish]]
- [[2026-06-12 - Release v0.4.0-preview TUI-first]]
