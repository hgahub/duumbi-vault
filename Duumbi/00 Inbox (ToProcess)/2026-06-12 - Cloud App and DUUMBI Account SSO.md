---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/medium
  - duumbi/complexity/high
created: 2026-06-12
milestone: M6
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Cloud App and DUUMBI Account (SSO)

## Context

Surface #4: hosted Studio with a DUUMBI account, enabling true "continue your session on any device". The Loop plan already designed the account model worth reusing: central issuer `auth.duumbi.dev`, User/Identity/Organization/Team/Session/ApiToken concepts, GitHub/GitLab/Google/email login, `.duumbi.dev` session cookie, RBAC roles — and mandates that the existing registry auth must not remain a user island (token migration path required). Prerequisites: session kernel (M2), registry graph/session sync backend (M2), desktop parity (M3).

## Goal

app.duumbi.dev (hosted Studio) + auth.duumbi.dev (central SSO): log in, see your synced sessions and graph repositories, continue a session started in the TUI/Desktop — opt-in sync, local-first remains default.

## Subtasks

1. Central auth service: stand up auth.duumbi.dev per the Loop plan's SSO section (OIDC issuer; GitHub OAuth first, Google + email magic link next); decide repo home (start inside duumbi-loop workspace or separate `duumbi-auth`).
2. Registry migration: duumbi-registry becomes an OIDC client of the central issuer; preserve existing registry API tokens in migration mode.
3. Hosted Studio: multi-tenant deployment of the Studio frontend backed by the graph-aware duumbi-registry (workspaces, sessions, snapshots per user/org); strict tenant scoping on every query (Loop plan rule: no data-layer method without org scope).
4. Session sync GA: implement the opt-in push/pull spec from [[2026-06-12 - Session Kernel and Event Ledger]]; device list + revocation; end-to-end encryption decision for ledgers.
5. Execution model decision: cloud builds/runs (sandboxed workers) vs. query-and-review-only cloud with execution staying local — recommend query/review-first, execution later.
6. Infra: new Pulumi stack (Container Apps, PostgreSQL, Key Vault, budgets) following existing duumbi-infra patterns; cost guardrails (current budget cap is $20/month — needs a deliberate raise decision).
7. Privacy/security docs: what syncs, what never leaves the machine, retention, deletion.

## Acceptance criteria

- One DUUMBI account logs into registry, repository, and app.duumbi.dev.
- Demo: start session in TUI on machine A → continue in cloud Studio → continue in Desktop on machine B.
- Tenant isolation tests pass; security/data-retention docs published.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Desktop App Packaging]]
