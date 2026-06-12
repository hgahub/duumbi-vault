---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/medium
  - duumbi/importance/medium
  - duumbi/complexity/medium
created: 2026-06-12
milestone: M3
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Desktop App Packaging

## Context

Studio already exists as a Leptos 0.8 SSR app (3-panel layout, WebSocket `/ws/chat`, graph view). The archived roadmap's guidance stands: build a Studio web/desktop path that reuses one frontend instead of splitting product surfaces. Desktop is surface #3 after CLI and TUI.

## Goal

DUUMBI Desktop = the existing Studio frontend packaged as an installable desktop app (recommendation: Tauri shell embedding the Studio server/webview), sharing the session kernel with CLI/TUI.

## Subtasks

1. Architecture spike: Tauri + sidecar `duumbi studio` server vs. Tauri + Leptos CSR build vs. plain webview wrapper. Pick based on Leptos SSR constraints; document trade-offs.
2. Local integration: workspace picker, native file dialogs, OS keychain for provider API keys, deep links (open intent/session).
3. Session kernel adoption: desktop reads/writes the same ledger as TUI/CLI ([[2026-06-12 - Session Kernel and Event Ledger]] is a prerequisite) — switching between TUI and Desktop mid-session on one machine is the M3 demo.
4. Packaging & signing: macOS notarized .dmg, Windows installer, Linux AppImage/deb; auto-update channel aligned with the release pipeline from [[2026-06-12 - Release v0.4.0-preview TUI-first]].
5. Keep one frontend codebase: no desktop-only UI forks; desktop-specific behavior behind a platform service trait.

## Acceptance criteria

- Installable signed builds for macOS/Windows/Linux from CI.
- Start a session in the TUI, continue it in the Desktop app on the same machine (local ledger, no cloud needed).
- No divergence between Studio-in-browser and Studio-in-desktop features.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
- [[2026-06-12 - Cloud App and DUUMBI Account SSO]]
