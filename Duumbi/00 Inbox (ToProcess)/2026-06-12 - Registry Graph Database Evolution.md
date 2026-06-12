---
tags:
  - duumbi/inbox/roadmap
  - duumbi/status/to-process
  - duumbi/classification/execution
  - duumbi/value/high
  - duumbi/importance/high
  - duumbi/complexity/high
created: 2026-06-12
milestone: M2
source: "[[DUUMBI Future Development Roadmap Map]]"
---

# Registry Graph Database Evolution

## Context

Today `hgahub/duumbi-registry` (deployed at registry.duumbi.dev) stores opaque `.tar.gz` module packages with a SQLite index — it distributes modules but does not understand them. The roadmap needs a durable store that understands the graphs themselves: snapshots, semantic hashes, queries, similarity features — the substrate for reuse (M5), Loop knowledge (M3), and session sync (M6). **No new service is planned**: this capability is built as the evolution of the existing duumbi-registry, keeping one deployment, one auth, one repo.

## Goal

duumbi-registry grows from a package store into a graph-aware registry: it stores versioned, immutable semantic graph snapshots with semantic hashes alongside the existing package archives, and serves a controlled graph query API — DUUMBI-native programs first (no git, no tree-sitter: the JSON-LD graph IS the source).

## Subtasks

1. Data model extension: graph snapshot record (snapshot id, module id, version, semantic hash, schema version, node/edge counts, provenance: intent id / session id / evidence links) added to the registry's SQLite schema via its existing migration mechanism; snapshots stored next to the `.tar.gz` archives.
2. Publish path: `duumbi publish` uploads the JSON-LD graph snapshot together with the package archive; backfill snapshots for already-published stdlib modules.
3. Query API: summary, node/symbol search, neighborhood, dependency closure — controlled query DSL, no raw query injection; this also backs the `query` surface remotely. Storage stays SQLite + JSON-LD blobs + extracted index tables, behind an adapter trait so the backend can change later without API breakage.
4. Semantic-hash index: content-addressed function/module hashes for dedup and reuse lookup (foundation for [[2026-06-12 - Semantic Graph Similarity and Reuse]] and proof caching in [[2026-06-12 - Compositional Verification Proof Boundaries]]).
5. Session ledger storage: implement the opt-in sync backend specified in [[2026-06-12 - Session Kernel and Event Ledger]] as a registry capability (per-user encrypted ledger blobs; local-first remains the default).
6. Infra: extend the existing `registry` stack in `duumbi-infra` (storage quota, probes, alerts) — no new Pulumi stack; watch the 1 GiB file-share quota and the $20/month budget cap, raise deliberately if needed.
7. CLI integration: `duumbi search --semantic` (by hash), "what depends on X" remote queries, snapshot push/pull.
8. Community evidence metadata: published versions carry eval results, verification status, and provider/model compatibility evidence; opt-in anonymized aggregate stats (which model families succeed at which task types) — the community half of [[2026-06-12 - Active Learning Loop]]. Today `VersionInfo` carries only integrity hash + yanked flag.

## Acceptance criteria

- registry.duumbi.dev stores graph snapshots for the stdlib modules and flagship examples alongside their packages.
- `duumbi` CLI can push a graph snapshot and query "what depends on X" remotely.
- Semantic-hash lookup answers "has an equivalent function already been published?".
- Existing package install/publish flows keep working unchanged (backward compatible).

## Links

- [[DUUMBI Future Development Roadmap Map]]
- [[2026-06-12 - Session Kernel and Event Ledger]]
- [[2026-06-12 - Semantic Graph Similarity and Reuse]]
- [[2026-06-12 - DUUMBI Loop Native Workflow Adaptation]]
- [[2026-06-12 - Token Economics Benchmark]] (registry reuse is the largest token-cost lever)
