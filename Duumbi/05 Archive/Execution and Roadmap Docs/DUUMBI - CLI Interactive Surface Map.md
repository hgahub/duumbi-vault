---
tags:
  - project/duumbi
  - doc/technical
status: draft
created: 2026-04-22
updated: 2026-04-22
related_maps:
  - "[[DUUMBI Technical Architecture Map]]"
  - "[[DUUMBI - Tools and Components]]"
  - "[[DUUMBI Roadmap Map]]"
---
# DUUMBI — CLI Interactive Surface Map

Reference map for the current `duumbi` interactive terminal interface. This note documents the actual `ratatui` REPL surface implemented in `src/cli/app.rs` and `src/cli/repl.rs`, not the older `reedline`-era descriptions still present in some phase notes.

![[DUUMBI - CLI Interactive Surface Map.excalidraw|DUUMBI CLI Interactive Surface Map]]

## Purpose

Use this note as the canonical naming map when discussing where new CLI information should appear.

Examples:
- "Show the active provider in `REPL-10 Status Bar`."
- "Add intent warnings to `REPL-06 Mode Strip`."
- "Render inline command suggestions in `REPL-11 Slash Command Menu`."

## Surface Model

The interactive UI has two main surfaces:

1. **Main REPL Surface**
The persistent terminal layout used during normal interaction.

2. **Bottom Interactive Panel Surface**
The transient lower panel opened by `/provider`, including the provider-management wizard.

## Main REPL Components

### `REPL-01 Header Hint`
Top hint line shown only when the output area is empty.

- Purpose: startup branding and first-use hint
- Typical content: app name, version, `/help` hint, exit hint
- Source: `src/cli/app.rs:994-1013`

### `REPL-02 Empty-State Tip`
Onboarding tip block shown in empty or uninitialised workspaces.

- Purpose: teach the first useful action
- Typical content: `/init`, `/intent create`, or first mutation suggestion
- Source: `src/cli/app.rs:1015-1047`

### `REPL-03 Conversation Pane`
Scrollable output and transcript area.

- Purpose: primary interaction history
- Shows: submitted prompts, slash commands, AI output, help text, status messages, errors
- Important rule: submitted input is echoed here after execution begins
- Source: `src/cli/app.rs:1049-1133`, `src/cli/repl.rs:156-175`, `src/cli/repl.rs:198-204`

### `REPL-04 Activity Spinner Overlay`
Transient `Working...` overlay rendered on top of the conversation area.

- Purpose: indicate async work in progress
- Shows: background build, LLM, or command activity state
- Source: `src/cli/app.rs:1103-1120`

### `REPL-05 Output Scroll Indicator`
Small overlay in the conversation pane when the user has scrolled upward.

- Purpose: show relative scroll position
- Source: `src/cli/app.rs:1122-1132`

### `REPL-06 Mode Strip`
Single-line mode indicator above the prompt area.

- Purpose: session mode context
- Left side: mode switch hint and current mode label
- Right side: focused intent slug, if present
- Source: `src/cli/app.rs:1135-1163`

### `REPL-07 Prompt Separator Top`
Upper horizontal divider between mode strip and prompt.

- Purpose: structural separation only
- Source: `src/cli/app.rs:1165-1176`

### `REPL-08 Prompt Input`
Single-line input field with the `❯` prompt prefix.

- Purpose: live text entry before submission
- Shows: the text currently being typed by the user
- Important rule: this is where commands exist before submit; after submit they move to `REPL-03`
- Source: `src/cli/app.rs:1178-1190`

### `REPL-09 Prompt Separator Bottom`
Lower divider between the prompt and status area.

- Purpose: structural separation only
- Source: `src/cli/app.rs:1165-1176`

### `REPL-10 Status Bar`
Persistent bottom status line of the main surface.

- Purpose: always-visible global session context
- Left side: current time and workspace path
- Right side: workspace name and provider connection status
- Important rule: concrete model names are not user-facing; Duumbi selects them internally per task
- Source: `src/cli/app.rs:1192-1240`

### `REPL-11 Slash Command Menu`
Inline menu shown below the status bar while typing slash commands.

- Purpose: command discovery and selection
- Shows: matching slash commands, descriptions, highlighted selection, scroll position
- Source: `src/cli/app.rs:1243-1304`, `src/cli/completion.rs:11-54`

## Interactive Panel Components

### `REPL-12 Provider Connection Panel`
Bottom panel opened by `/provider`.

- Purpose: provider and credential management
- Shows: provider list, selected provider, role, key state, panel actions
- Important rule: this occupies the bottom interactive zone and replaces the slash menu while open
- Source: `src/cli/repl.rs:273-288`, `src/cli/app.rs:1306-1635`

### `REPL-13A Provider Kind Step`
Wizard step for selecting provider family.

- Purpose: choose `anthropic`, `openai`, or other supported provider kind
- Source: `src/cli/mode.rs:115-120`, `src/cli/app.rs:1398-1426`

### `REPL-13C Auth Type Step`
Wizard step for selecting authentication mechanism.

- Purpose: choose API key versus subscription token flow
- Source: `src/cli/mode.rs:129-137`, `src/cli/app.rs:1475-1522`

### `REPL-13D Credential Entry Step`
Wizard step for entering the API key or subscription token.

- Purpose: securely capture the credential
- Shows: expected environment variable and masked input
- Source: `src/cli/mode.rs:138-148`, `src/cli/app.rs:1523-1590`

### `REPL-13E Credential Storage Confirm Step`
Wizard step for selecting where the credential should be stored.

- Purpose: choose persistent file storage versus session-only storage
- Source: `src/cli/mode.rs:149-159`, `src/cli/app.rs:1591-1611`

## Placement Rules

### 1. Always-visible global state
Place in `REPL-10 Status Bar`.

Examples:
- provider connection status
- workspace identity
- future provider badge
- future connection state

### 2. Session or mode context
Place in `REPL-06 Mode Strip`.

Examples:
- focused intent
- agent versus intent state
- future temporary context badges

### 3. Historical or command-result output
Place in `REPL-03 Conversation Pane`.

Examples:
- help output
- build summaries
- AI replies
- command errors
- session history

### 4. Live user typing and pre-submit state
Place in `REPL-08 Prompt Input` or `REPL-11 Slash Command Menu`.

Examples:
- unfinished slash commands
- inline completion
- future argument hints

### 5. Temporary overlays for ongoing work
Prefer overlay treatment instead of pushing extra transcript lines.

Primary location:
- `REPL-04 Activity Spinner Overlay`

### 6. Multi-step selection or configuration flows
Place in `REPL-12` and `REPL-13*`, not in the conversation pane.

Examples:
- provider setup
- future permission or session picker UI

## Where Things Appear

### Provider connection status
- Primary location: `REPL-10 Status Bar`
- Secondary locations: `REPL-12 Model Selector Panel`, `REPL-13B Model Pick Step`, `REPL-13D Credential Entry Step`
- Additional textual output: `/status` in `REPL-03 Conversation Pane`

### Typed commands before submit
- `REPL-08 Prompt Input`

### Submitted commands and natural-language requests
- echoed into `REPL-03 Conversation Pane`

### Workspace name and workspace path
- `REPL-10 Status Bar`

### Focused intent
- `REPL-06 Mode Strip`

### Slash command discovery
- `REPL-11 Slash Command Menu`

### In-progress work state
- `REPL-04 Activity Spinner Overlay`

## Source References

- Full layout render: `src/cli/app.rs:869-987`
- Output pane: `src/cli/app.rs:1049-1133`
- Mode strip: `src/cli/app.rs:1135-1163`
- Prompt input: `src/cli/app.rs:1178-1190`
- Status bar: `src/cli/app.rs:1192-1240`
- Slash menu: `src/cli/app.rs:1243-1304`
- Provider panel and wizard: `src/cli/app.rs:1306-1635`
- Slash command source list: `src/cli/completion.rs:11-54`
- Input submission echoing: `src/cli/repl.rs:156-175`, `src/cli/repl.rs:198-204`
- `/provider` panel open flow: `src/cli/repl.rs:273-288`

## Related

- [[DUUMBI - Phase 4 - Interactive CLI & Module System]]
- [[DUUMBI - Phase 11 - CLI UX & Developer Experience]]
- [[DUUMBI - Tools and Components]]


## Detailed Screen Specs

This map gives the **overview**. The per-screen specifications — with sub-element tables, state diagrams, interactions, and design rules — live as sibling notes and are catalogued here:

- [[DUUMBI CLI Screen - _Index]] — index of all screen specs and flows
- [[DUUMBI CLI Screen - _Design System]] — colour semantics, skeleton decomposition, diagram layout
- [[DUUMBI CLI Screen - _User Journeys]] — catalogue of documented user flows

Completed spec notes:
- [[DUUMBI CLI Screen - REPL-01 Header Hint]]
- [[DUUMBI CLI Screen - REPL-10 Status Bar]]
- [[DUUMBI CLI Screen - REPL-12 Provider Connection Panel]]
- [[DUUMBI CLI Flow - First Launch]]

Remaining (not yet written): REPL-02, REPL-03, REPL-04, REPL-05, REPL-06, REPL-07, REPL-08, REPL-09, REPL-11, REPL-13A, REPL-13C, REPL-13D, REPL-13E; flows *Slash Command Discovery*, *Provider Setup via /provider*, *Intent Create & Focus*.
