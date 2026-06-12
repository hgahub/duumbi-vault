---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/low
created: 2026-06-12
milestone: M0
source: "[[DUUMBI Future Development Roadmap Map]]"
related_issues:
  - hgahub/duumbi#686
---

# Docs Truth Reconciliation

## Context

docs.duumbi.dev (Starlight, in `hgahub/duumbi-web`) documents `cargo install duumbi`, but DUUMBI is **not published to crates.io** and has no releases. The main repo still carries legacy `sites/` + `docs/` content alongside the canonical duumbi-web docs (issue #686). Docs also pin an outdated model id (`claude-sonnet-4-20250514`) in provider setup examples.

## Goal

Every public claim (install path, feature status, model ids, registry behavior) matches shipped reality at v0.4.0-preview launch. No aspirational instructions.

## Subtasks

1. Decide: publish `duumbi` to crates.io (name is presumably reserved by us — verify) **or** rewrite installation docs around GitHub Releases binaries + install script. Document the decision.
2. Audit all pages in duumbi-web docs + landing + compare pages for stale claims (install, providers, model ids, registry, platform support).
3. Execute #686: migrate/refresh/delete legacy `sites/` and `docs/` content in the main repo; main repo docs become contributor-facing only, duumbi-web is the canonical user docs.
4. Add a "Status & roadmap" page sourced from this vault's roadmap map, with honest Delivered/Partial/Research labels (mirror [[DUUMBI - Service and Research Direction]]).
5. Add a docs CI check or release-checklist item: install instructions must be verified against the latest release before publishing.

## Acceptance criteria

- Following the docs verbatim on a clean machine yields a working install (this is also an M0 release gate).
- No page references unpublished distribution channels or retired model ids.
- Issue #686 closed.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Release v0.4.0-preview TUI-first]]
