---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M6
target_window: H2 2027
source: "/Users/heizergabor/space/hgahub/duumbi-loop/wiki/duumbi-loop-codex-task.md"
depends_on:
  - M2
  - M3
  - M5
---

# DUUMBI Loop Cloud Service Milestone Plan

## Context

The prepared `duumbi-loop` task is the full hosted DUUMBI Loop service plan: Axum API, webhook receiver, async workers, metadata store, code provider integrations, graph snapshots, LLM/model policy, dashboard, billing, Slack, and intake/spec/review/closure artifacts.

I reviewed the current inbox notes and [[DUUMBI Future Development Roadmap Map]]. The roadmap already separates:

- M2: session kernel and graph-aware registry.
- M3: DUUMBI-native Loop adaptation and Desktop App.
- M5: importing existing source repositories into a semantic knowledge graph.
- M6: Cloud App, DUUMBI account/SSO, and v1.0 hosted-service readiness.

This ticket is not the narrow M3 native Loop adaptation. It is the hosted cloud service and external provider productization layer around Loop. It should reuse the original draft, but the draft must be rewritten around milestones rather than an MVP.

## Roadmap insertion

Insert this as an M6 workstream in H2 2027, before v1.0 GA:

> DUUMBI Loop Cloud Service: hosted intake/spec/review/closure for DUUMBI-native workspaces and optional GitHub/GitLab adapters, backed by `duumbi-auth`, Stripe billing, Neon Postgres, Postgres-backed graph tables, regional LLM policy, source-retention controls, Slack/email notifications, and automatic closure publishing.

Discovery and document rewrite can start after M3, but implementation should wait until the M2/M3/M5 dependencies are real. Building it earlier would pull the product back toward a GitHub/GitLab-first model and would duplicate the graph/session responsibilities that the registry roadmap already assigns to M2.

## Milestone framing

Do not frame this as an MVP. Use milestone slices:

1. M6.0: Discovery and plan rewrite after M3. Update `wiki/duumbi-loop-codex-task.md` or supersede it with a v2 milestone document. Confirm `duumbi-infra`, `duumbi-registry`, `duumbi-web`, and `duumbi-auth` responsibilities.
2. M6.1: Account, tenant, and billing shell. Stand up `duumbi-auth`, org/team/RBAC, Stripe customer/subscription/entitlement records, email magic-link login, and dashboard settings.
3. M6.2: Data and graph foundation. Use Neon Postgres for metadata and first graph backend. Add retention settings, deletion jobs, audit logs, model policy, regional LLM data policy, and cost controls.
4. M6.3: External provider adapters. GitHub App first; GitLab Cloud next; GitLab Self-Hosted with manual webhook setup first. Keep DUUMBI-native provider as primary.
5. M6.4: Cloud workflow execution. Hosted intake/spec/review/closure artifacts, Slack/email notifications, dashboard run detail, branch/PR failure handling, review comment mode, and automatic closure publish.
6. M6.5: Hardening and launch. Security docs, data-retention docs, regional policy docs, pricing page, observability, operational budgets, and v1.0 readiness evidence.

## Decisions

### Repository topology

- `hgahub/duumbi-loop`: hosted Loop service, provider adapters, workflow artifacts, dashboard backend, workers.
- `hgahub/duumbi-auth`: central DUUMBI auth service from the start. Do not start auth inside `duumbi-loop` and move it later.
- `hgahub/duumbi-infra`: DNS, Container Apps/SWA patterns, secret references, observability, budget guardrails. Do not add Azure Database for PostgreSQL as the default Loop database.
- `hgahub/duumbi-web`: `loop.duumbi.dev`, `docs.duumbi.dev/loop`, pricing/docs/launch content.
- `hgahub/duumbi-registry`: graph-aware registry, session sync, graph snapshot publish/read path.

### Database

Default database: Neon Postgres.

Reasoning:

- The introductory period needs a real free-tier database and controlled scale-up path, not Azure Database for PostgreSQL.
- Neon currently offers a free plan with 100 CU-hours per month per project, 0.5 GB storage per project, scale-to-zero behavior, and usage-based paid plans.
- Supabase is viable, but its integrated Auth/Storage bundle is less useful if `duumbi-auth` is separate and Stripe is the billing default.
- Store the Neon connection string as a secret reference, not in frontend env or Pulumi plaintext.

### Graph backend

Default graph backend: Postgres-backed property graph tables in the same Neon database for the first cloud milestones.

Initial shape:

- `graph_snapshots`
- `graph_nodes`
- `graph_edges`
- `graph_node_attributes`
- `graph_edge_attributes`
- `graph_symbol_index`
- `graph_embedding_index`
- optional materialized views for common neighborhoods and dependency closures

Use Postgres full-text search and `pgvector` where available for search/similarity. Keep `codegraph-core` behind an adapter interface so Neo4j, Kuzu, or another graph backend can be added later based on measured query needs.

Do not make Neo4j Aura the default first backend. It has a free learning/prototyping tier, but the paid professional path starts high enough that it conflicts with the low-cost introduction requirement. Do not make Kuzu a hard dependency yet either; it remains technically attractive, but the upstream KuzuDB repository was archived in 2025, so use it only as an optional snapshot/analytics adapter unless the project direction is clarified.

### Billing

Stripe is the default billing provider.

Required M6 records:

- `billing_customers`
- `billing_subscriptions`
- `billing_entitlements`
- `billing_events`
- `usage_meter_events`

Stripe webhook processing must be idempotent and tenant-scoped.

### Login

Email login uses magic links. Password login is not in the first cloud milestone unless a later security/product decision explicitly adds it.

`duumbi-auth` should also support GitHub/GitLab/Google identity links, but email magic link is the default email flow.

### Source retention

Default raw repo snapshot retention: 7 days.

Make it configurable per organization and repository in Settings:

- `0 days`: ephemeral clone only; delete immediately after successful indexing.
- `7 days`: default.
- `14 days`
- `30 days`
- `90 days`: admin-only, with warning.

Graph snapshots, run artifacts, audit events, and source references are separate retention classes. Raw source retention must have a cleanup job, deletion audit events, and UI visibility.

### LLM data policy

Yes: enforce region-aware policy buckets.

Required first regions:

- EU
- USA
- China

Org admins must be able to configure allowed providers/models by region, prompt/response retention, code-snippet retention, max context size, and whether cross-region fallback is allowed. The context builder must still minimize source sent to LLMs and must never send a full repo dump by default.

### GitLab Self-Hosted

Manual webhook setup is enough for the first GitLab Self-Hosted milestone.

The UI should generate setup instructions, webhook URL, secret, event checklist, and a connection test. Base URL validation and SSRF protections remain mandatory.

### Spec PR branch protection

If the target repository does not allow the DUUMBI bot to create a branch or PR:

- Mark the run `needs_action`.
- Show a dashboard banner with the exact blocked operation and required repo permission.
- Send email to org admins/repo owners.
- Send Slack notification if Slack is connected.
- Provide a fallback downloadable/manual spec bundle so the team can apply the files manually.

### Knowledge update

Closure should automatically publish knowledge updates by default.

Keep an org-level setting to downgrade to candidate-only mode for regulated teams, but the default product behavior is automatic publish on closure.

### Review strictness

Default review behavior is comment mode, even when blocking issues are found.

Blocking issues must be clearly labeled in the summary and inline comments, but the provider review state should not default to `changes_requested`. Add an org/repo setting for strict mode if a team wants blocking findings to become provider-native blocking reviews.

## duumbi-infra notes

The current `duumbi-infra` checkout has no README. The actual stack facts are:

- `stack-persistent.ts`: Key Vault, Log Analytics, subscription budget with a $20/month project cap.
- `stack-platform.ts`: Azure DNS for `duumbi.dev`, free Static Web Apps for web/docs, and a Slack approval bridge Function App.
- `stack-registry.ts`: registry Container App, Azure Files-backed `/data`, 1 GiB file share, Log Analytics wiring, custom `registry.duumbi.dev` domain, and scale-to-zero/min-0 deployment.
- No current Loop database, graph database, Service Bus, or Loop Container App stack exists.

M6 infra should follow the existing Pulumi split, but the Loop database should be Neon during introduction. Azure should remain DNS/hosting/secrets/observability unless a later paid-infra decision overrides this.

## Acceptance criteria

- The original `duumbi-loop` task is rewritten or superseded without MVP language.
- `duumbi-auth` exists as a separate repo/service contract.
- Neon Postgres is the default metadata database; no Azure PostgreSQL dependency is introduced for the introductory period.
- Graph storage starts on Postgres-backed graph tables with adapter boundaries for later graph engines.
- Stripe billing, email magic links, regional LLM data policy, source-retention settings, and automatic closure publishing are explicit requirements.
- GitHub/GitLab provider flows are optional adapters around DUUMBI-native Loop, not the primary product model.
- GitLab Self-Hosted works with manual webhook setup first.
- Branch/PR permission failures produce UI, email, and Slack notifications with a manual fallback.
- Blocking review findings default to comment mode unless strict mode is enabled.

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]]
- [[2026-06-12 - Cloud App and DUUMBI Account SSO]]
- [[2026-06-12 - Registry Graph Database Evolution]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
- [[2026-06-12 - Code Import to Semantic Graph]]
- [[2026-06-12 - Active Learning Loop]]
- [[2026-06-12 - Model Capability Advisor and Task Routing]]
- [[2026-06-12 - Effort Levels and Cost Control]]

## External facts checked

- Neon pricing/free plan: https://neon.com/pricing
- Supabase billing/free plan: https://supabase.com/docs/guides/platform/billing-on-supabase
- Neo4j pricing: https://neo4j.com/pricing/
- KuzuDB repository status and license: https://github.com/kuzudb/kuzu
