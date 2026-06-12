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

# TUI Provider Setup and Credential Discovery UX

## Context

Manual review used the MiniMax credential in `/Users/heizergabor/.duumbi/credentials.toml`. The fresh workspace had no provider configuration, so `/status` and `/provider` reported providers as not configured even though a MiniMax key existed in the user credential file. After adding a workspace provider entry manually and launching with `MINIMAX_API_KEY` loaded, Query mode worked against MiniMax.

This creates a first-run setup gap: users can have a credential available but still see only "not configured" unless they understand config layering and env indirection.

## Goal

The TUI provider flow should guide users from detected credential material to a tested, active provider without requiring manual TOML edits or hidden environment setup.

## Observed Evidence

- `~/.duumbi/credentials.toml` contained `MINIMAX_API_KEY`.
- Fresh workspace `/status`: `Providers: not configured (source: none)`.
- `/provider` listed MiniMax as `not configured / missing / api key`.
- `duumbi provider add minimax MINIMAX_API_KEY` attempted to save through user config and failed under sandboxed user-config access.
- Manual workspace config with `provider = "minimax"` and `role = "primary"` plus env injection made Query mode succeed.

## Subtasks

1. Define the intended relationship between `credentials.toml`, user config, workspace config, and environment variables.
2. In `/provider`, distinguish "credential found but provider not enabled" from "credential missing".
3. Add a provider setup action that enables MiniMax from the existing credential file and runs a connection test.
4. Prevent casing footguns in hand-authored config by documenting valid values and, where possible, producing repair suggestions for `MiniMax` -> `minimax` and `Primary` -> `primary`.
5. Ensure `/status` reports the config source and credential state without exposing secrets.

## Acceptance Criteria

- A user with `MINIMAX_API_KEY` in `~/.duumbi/credentials.toml` can enable and test MiniMax from the TUI.
- Provider setup never prints or stores raw secrets in screen output, logs, or session history.
- `/status` and `/provider` use consistent provider-state language.
- Provider setup works in a fresh workspace without editing TOML by hand.

## Links

- [[2026-06-12 - TUI as Primary Surface Polish]]
- [[2026-06-12 - Model Capability Advisor and Task Routing]]
- [[2026-06-12 - Docs Truth Reconciliation]]
