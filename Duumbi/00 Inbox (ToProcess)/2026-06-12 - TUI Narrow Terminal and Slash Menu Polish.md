---
tags:
  - duumbi/inbox/enriched
  - duumbi/status/processed
  - duumbi/classification/execution
  - duumbi/value/medium
  - duumbi/importance/high
  - duumbi/complexity/medium
duumbi_inbox_enrichment: processed
duumbi_inbox_enrichment_generated_at: 2026-06-26T18:37:09.212Z
---

# TUI Narrow Terminal and Slash Menu Polish

<!-- duumbi-inbox-enrichment:v1 status=processed generated_at=2026-06-26T18:37:09.212Z -->

## Source
- Surface: Manual Obsidian edit
- Vault path: Duumbi/00 Inbox (ToProcess)/2026-06-12 - TUI Narrow Terminal and Slash Menu Polish.md
- Submitted by: unknown unless explicit in the raw input

## Raw input
> ---
> tags:
>   - duumbi/inbox/roadmap
>   - duumbi/status/to-process
>   - duumbi/classification/execution
>   - duumbi/value/medium
>   - duumbi/importance/high
>   - duumbi/complexity/medium
> created: 2026-06-12
> milestone: M0
> source: "Manual TUI UX review, 2026-06-12"
> parent: "[[2026-06-12 - TUI as Primary Surface Polish]]"
> ---
> 
> # TUI Narrow Terminal and Slash Menu Polish
> 
> ## Context
> 
> Manual review launched the TUI in a narrow terminal (`stty cols 60 rows 18`). The UI remained usable, but several elements degraded in ways that make the first-release surface feel unfinished: footer text clipped mid-word, command descriptions truncated without clear indication, the prompt lost its bordered input box, and the bottom `cwd` value was clipped aggressively.
> 
> This is a robustness and polish subtask under M0, not a separate product initiative.
> 
> ## Goal
> 
> The TUI should degrade intentionally on small terminals: compact layout, predictable truncation, no misleading half-controls, and a clear minimum-size message when the screen is too small for the full interface.
> 
> ## Observed Evidence
> 
> - Test size: 60 columns x 18 rows.
> - First screen remained functional, but the top help text lost the `Ctrl-D` exit hint.
> - Slash-menu footer showed truncated text such as `Esc clo`.
> - Command descriptions cut off mid-word, e.g. registry/resume descriptions.
> - Prompt rendered as a bare input line instead of the normal bordered input box.
> - Footer path shortened to an unclear `/pri….Xk` fragment.
> 
> ## Subtasks
> 
> 1. Define minimum supported terminal dimensions for the full TUI.
> 2. Add compact layout rules for narrow/short terminals: shorter header, condensed footer, single-line status, and no half-rendered controls.
> 3. Use consistent ellipsis/truncation helpers for descriptions, footer labels, cwd, and hints.
> 4. Add render tests at 60x18 and one smaller unsupported size.
> 5. If below minimum size, show a clear "terminal too small" message with the required dimensions.
> 
> ## Acceptance Criteria
> 
> - At 60x18, the UI remains readable and controls do not truncate into misleading partial words.
> - Below the supported minimum size, the user gets a clear size requirement instead of a broken layout.
> - Slash-menu filtering and `/resume` remain usable in compact mode.
> 
> ## Links
> 
> - [[2026-06-12 - TUI as Primary Surface Polish]]
> - [[2026-06-12 - Release v0.4.0-preview TUI-first]]

## Interpreted intent

Improve the TUI interface to gracefully degrade on narrow/small terminals, with consistent truncation, compact layout, and a clear minimum-size message, as a robustness and polish task under M0.

## Developer summary

The TUI currently shows clipped footer text, truncated command descriptions, a bare prompt input line, and a shortened working-directory path when launched in a narrow terminal (60x18). This task adds compact layout rules, safe truncation with ellipsis, a minimum-size check with a clear error, and render tests at 60x18. Implementation will likely involve a new responsive module or changes to existing layout code in the TUI source, using the ratatui/crossterm dependency.

## UML overview

```mermaid
flowchart TD
    A[Terminal size changed] --> B{Width >= MIN_WIDTH<br/>Height >= MIN_HEIGHT?}
    B -- No --> C[Show "terminal too small" message with required dimensions]
    B -- Yes --> D{isCompactMode needed?}
    D -- No --> E[render full layout]
    D -- Yes --> F[render compact layout]
    F --> G[shorter header, condensed footer, single-line status]
    G --> H[apply truncation with ellipsis to footer, cwd, descriptions, hints]
    H --> I[ensure slash-menu filtering and /resume remain usable]
    I --> J[update tests at 60x18 and one unsupported size]
```

## Classification
- Type: execution
- Business value: medium
- Importance: high
- Complexity: medium

## Clarifications
### Answered
- The test terminal size for observed issues is 60 columns x 18 rows.
- Footer text clipped mid-word (e.g., 'Esc clo').
- Command descriptions are cut off mid-word.
- Prompt rendered as a bare input line instead of a bordered input box.
- Footer working directory shortened to '/pri….Xk' or similar fragment.
- Top help text lost the Ctrl-D exit hint.
- The note is a subtask under M0 parent '2026-06-12 - TUI as Primary Surface Polish'.

### Open
- What exact minimum supported dimensions should be chosen (e.g., 80x24)?
- Should compact mode be triggered only by width, or also by height?
- How should truncation behave for slash-menu command descriptions? Fast-and-short vs. prioritized?
- Should the prompt input box be resized or completely removed below a certain height?
- Should certain UI elements be hidden entirely rather than compacted?
- What is the acceptable truncation pattern for the working directory (always leading ellipsis, trailing ellipsis, or smart path shortening)?

## Relevant DUUMBI context
- Parent note: '2026-06-12 - TUI as Primary Surface Polish' (vault) – umbrella for TUI polish.
- Source code dependency on ratatui 0.30.2 and crossterm 0.29.0 (Cargo.toml) – the UI library in use.
- Source code likely resides in src/cli/ (TUI entry) or a dedicated tui module.
- DUUMBI - Glossary, DUUMBI - Agentic Development Runbook (vault) – workflow and common terminology.

## Related GitHub context

triage should verify later – no known GitHub issues or PRs directly linked to this note; a parent issue for the umbrella polish task may already exist.

## Initial routing recommendation

GitHub issue

## Requested follow-up
- Perform Stage 4 triage to create a GitHub issue, or attach to existing parent issue for TUI polish.
- Ensure the GitHub issue describes the developer summary, acceptance criteria, and references this enriched note.
- Assign to M0 milestone and label as 'tui', 'ux', 'compact-mode'.

## AI agent instructions
- Create a new GitHub issue in hgahub/duumbi titled 'TUI Narrow Terminal and Slash Menu Polish'.
- Use the developer_summary for the issue description.
- Include the Mermaid diagram as-is in the issue body.
- Link to this vault note for full context and acceptance criteria.
- Define minimum dimensions (suggest 80x24) and compact behavior based on clarifications_open.
- Propose code changes: likely new file `src/tui/responsive.rs` with helpers for size check, compact layout, and truncation.
- List test cases: render at 60x18, 79x23 (just above minimum), 80x24 (minimum), 40x10 (too small), and verify output.
- Add a note that the slash menu must remain functional in compact mode.
- Request no implementation in this stage – only accept the issue after human approval.

## Scope candidate
### In
- TUI compact layout for narrow/short terminals.
- Consistent ellipsis/truncation helpers for footer, cwd, command descriptions, and hints.
- Minimum-size detection and a clear error message.
- Render tests at 60x18 and one unsupported size.
- Ensure slash-menu filtering and /resume work in compact mode.

### Out
- Full TUI redesign or new features.
- Performance improvements unrelated to layout.
- Changes to non‑TUI CLI output.
- Mobile or web surfaces.

## Risks and trade-offs
- Choosing the wrong minimum dimensions could make the TUI unusable on many developer terminals (e.g., 80x24 is standard but some use 80x25).
- Aggressive truncation may hide critical information (e.g., full command description needed for decision).
- Compacting the slash menu description may conflict with future additions to the slash command list.
- If the prompt input box is removed, users may mistake the interface for a plain CLI.

## Obsidian tags

#duumbi/inbox/enriched #duumbi/status/processed #duumbi/classification/execution #duumbi/value/medium #duumbi/importance/high #duumbi/complexity/medium

## Enrichment result
- Date: 2026-06-26T18:37:09.212Z
- Status: ready for triage
- Canonical duplicate: none verified
- Facts:
- The note is tagged as execution, medium value, high importance, medium complexity, M0 milestone.
- Tested at 60x18 with ratatui‑based TUI.
- Observed issues include clipped footer, truncated slash‑menu descriptions, missing bordered prompt, shortened cwd display, and lost Ctrl‑D hint.
- The parent note '2026-06-12 - TUI as Primary Surface Polish' exists in the vault.
- Assumptions:
- The TUI is implemented with ratatui/crossterm and currently lacks responsive layout code.
- The parent umbrella polish issue or project item already exists and this note is a breakdown of one of its subtasks.
- The acceptance criteria in the note are sufficient for a developer to begin implementation after clarifications are answered.
- Recommendations:
- Create a GitHub issue for this task and link it to the parent TUI polish umbrella.
- Before implementation, decide minimum dimensions (e.g., 80x24) and confirm compact-mode triggers.
- Write a new module `src/tui/responsive.rs` to keep layout adaptation logic separate.
- Use the existing ellipsis/truncation helper patterns from ratatui or build a simple one.
- Add snapshot or golden‑file tests for terminal sizes to prevent regressions.
