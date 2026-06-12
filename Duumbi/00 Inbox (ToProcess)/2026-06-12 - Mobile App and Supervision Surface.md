---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/medium
  - duumbi/importance/low
  - duumbi/complexity/medium
created: 2026-06-12
milestone: post-1.0
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Mobile App and Supervision Surface

## Context

Surface #5, post-1.0. The vault already records the working pattern: mobile-supervised delivery (Codex App, Slack as mobile capture surface). For DUUMBI the realistic first mobile value is **supervision, not authoring**: watch runs, answer clarifying questions, approve/reject patches and stage gates — continuing a session in the "decide" role while away from a keyboard. Requires Cloud App + session sync (M6) as backend.

## Goal

A mobile app (or installable PWA first) where a signed-in user sees active sessions/runs, receives push notifications on approval gates and questions, and can approve, reject, or answer — those decisions land in the same session ledger every other surface reads.

## Subtasks

1. Form-factor decision: PWA on top of app.duumbi.dev first (cheapest, reuses Studio) vs. native (Tauri Mobile / Flutter / Swift+Kotlin). Recommend PWA + push first; reassess native after usage data.
2. Supervision UX: session list, run timeline, question topics inbox, patch summary view (graph diff rendered as readable change description), approve/reject with audit trail.
3. Push notifications: needs-input, run completed/failed, approval requested; quiet hours.
4. Mobile capture: quick intent/idea capture into the inbox flow (text/voice), feeding intake — replaces the Slack-capture workaround.
5. Security: biometric unlock for approval actions; scoped mobile tokens; remote sign-out.
6. Explicit non-goals first version: no graph editing, no local builds on mobile.

## Acceptance criteria

- An approval requested in a TUI/cloud session can be granted from the phone within seconds, recorded in the ledger.
- Question topics can be answered from mobile and consumed by the next loop step.
- Capture-to-intake works end-to-end from mobile.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Cloud App and DUUMBI Account SSO]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
