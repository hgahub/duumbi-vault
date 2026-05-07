---
tags:
  - project/duumbi
  - release
status: active
created: 2026-03-26
updated: 2026-03-26
---
# DUUMBI Release Notes

> Canonical release history. Each version links to its GitHub Release.

---

![[demo.drawio.svgą]]
## v0.2.0 — Studio Workflow Redesign (2026-03-26)

**GitHub Release:** [v0.2.0](https://github.com/hgahub/duumbi/releases/tag/v0.2.0)
**PR:** [#490](https://github.com/hgahub/duumbi/pull/490)
**Milestone:** Phase 15 — Studio Workflow Redesign (13 issues, #477–#489)
**Branch:** `phase15/studio-workflow-redesign`

### Highlights

The Studio has been redesigned from an 8-step workflow to a **3-panel layout** that mirrors the natural development cycle: **Intents** (describe what you want), **Graph** (see it being built), **Build** (compile and run). This release also introduces automated GitHub Releases with pre-built binaries for 4 platforms.

### Breaking Changes

- Studio layout completely redesigned — previous 5-tab sidebar (Explorer/Intents/Registry) replaced by icon rail + intent tree sidebar
- CSS fully replaced with new design system (dark theme: #0e0f0f, accent: #c25a1a, fonts: Sora + JetBrains Mono)
- `SidebarTab` enum deprecated in favor of `ActivePanel { Intents, Graph, Build }`

### New Features

#### Studio 3-Panel Layout
- **Intents panel** — unified create, review, and execute in one view. Left sidebar shows intent list with status; right side shows detail view with description, acceptance criteria, task plan, and action buttons.
- **Graph panel** — split view with C4 graph visualization on the left and context-aware chat on the right. The chat knows which module and C4 level the user is viewing and enriches prompts accordingly.
- **Build panel** — one-click build and run. Shows compilation output, errors with node IDs, and binary execution stdout/stderr.

#### WebSocket Streaming Chat
- New `/ws/chat` endpoint replaces the fire-and-forget server function approach
- Streaming LLM responses appear in real-time as the model generates them
- Context-aware prompt assembly via `assemble_context()` (Phase 10)
- Session history maintained per WebSocket connection
- Live graph refresh after successful mutations — no page reload needed

#### Command Palette (Cmd+K)
- Replaces the previous search overlay and keyboard shortcuts overlay
- Unified access to: intents, graph nodes, provider configuration, theme toggle, registry search, agent templates
- Live filtering with grouped results (Intents / Nodes / Commands)

#### New Design System
- Dark theme: `#0e0f0f` background, `#c25a1a` orange accent
- Typography: Sora (body), JetBrains Mono (code)
- Icon rail (44px) with explorer and search buttons
- Resizable sidebar (160–420px) with intent tree showing C4 children
- Footer (52px) with 3 panel toggle buttons

#### Automated GitHub Releases
- New `.github/workflows/release.yml` triggered by version tags (`v*`)
- Builds release binaries for 4 platforms:
  - macOS ARM (aarch64-apple-darwin) — M1/M2/M3/M4
  - macOS Intel (x86_64-apple-darwin)
  - Linux x86_64 (x86_64-unknown-linux-gnu)
  - Linux ARM64 (aarch64-unknown-linux-gnu)
- Each archive contains: `duumbi` CLI + `studio` SSR server + `runtime/duumbi_runtime.c`
- Auto-generated release notes from commits

### New Server Functions
- `create_intent(description)` — LLM generates structured YAML spec from natural language
- `execute_intent(slug)` — full intent pipeline: decompose, mutate, verify
- `get_provider_list()` — returns configured LLM providers from `config.toml`
- `trigger_run()` — executes compiled binary, returns stdout/stderr

### New Components (9 files)
| File | Purpose |
|------|---------|
| `ws.rs` | WebSocket streaming chat handler |
| `icon_rail.rs` | 44px left navigation rail |
| `footer.rs` | 3-panel bottom navigation (Intents/Graph/Build) |
| `command_palette.rs` | Cmd+K overlay with search and commands |
| `panels/intents_panel.rs` | Intent create/review/execute |
| `panels/graph_panel.rs` | C4 graph + chat split view |
| `panels/build_panel.rs` | Compile and run output |
| `panels/mod.rs` | Panel module declarations |
| `release.yml` | GitHub Actions release workflow |

### Stats
- **+5,743 / −2,580** lines across 24 files
- **0** clippy warnings
- **1,747+** tests passing
- Version: `0.1.1` → `0.2.0` (both `duumbi` and `duumbi-studio`)

### Installation

```bash
# Download for your platform
curl -LO https://github.com/hgahub/duumbi/releases/download/v0.2.0/duumbi-v0.2.0-aarch64-apple-darwin.tar.gz

# Extract and install
tar xzf duumbi-v0.2.0-aarch64-apple-darwin.tar.gz
sudo cp duumbi-v0.2.0-aarch64-apple-darwin/duumbi /usr/local/bin/
sudo cp duumbi-v0.2.0-aarch64-apple-darwin/studio /usr/local/bin/

# Quick start
mkdir my-project && cd my-project
duumbi init
duumbi studio  # Opens Studio at http://localhost:8421
```

### Known Limitations
- WebSocket chat requires a configured LLM provider in `.duumbi/config.toml`
- Studio binary must be in the same directory as the `duumbi` binary (or on PATH)
- No Windows binaries yet (planned for Phase 16)

---

## v0.1.1 — Phase 12: Dynamic Agent System & MCP (2026-03-24)

**PR:** [#475](https://github.com/hgahub/duumbi/pull/475)
**Milestone:** Phase 12 — Dynamic Agent System & MCP (45 issues, #430–#474)

- MCP server with 10 tools (graph query, mutate, validate, describe)
- Dynamic agent assembly: task profiling → agent team selection → execution strategy
- Agent templates (Planner, Coder, Reviewer, Tester, Repair) with specializations
- Cost tracking with budget enforcement and circuit breaker
- Agent merger for multi-agent output consolidation
- MCP client for external server proxy
- ~6,157 new lines, ~180 new tests (1,261 → 1,747 total)

## v0.1.0 — Phases 0–11 (2026-03-19)

Initial release covering the core compiler, AI mutation pipeline, intent system, registry client, and CLI UX.

- JSON-LD → Cranelift → native binary compilation
- 35 Op types (arithmetic, control flow, strings, arrays, structs, ownership, error handling)
- AI graph mutation via Anthropic/OpenAI/Grok/OpenRouter with tool use
- Intent-Driven Development: describe → plan → execute → verify
- Multi-module workspaces with dependency resolution
- Registry client with SemVer resolution and lockfile
- Interactive REPL with completions, history, spinners
- C4 graph visualization (Leptos SSR)
- 1,259 tests, 0 clippy warnings

---

## Release Process

### Creating a Release

```bash
# 1. Merge the feature PR to main
gh pr merge <PR_NUMBER>

# 2. Pull latest main
git checkout main && git pull

# 3. Tag the release
git tag v0.2.0
git push --tags

# 4. GitHub Actions automatically:
#    - Builds binaries for 4 platforms (macOS ARM/Intel, Linux x86_64/ARM64)
#    - Creates GitHub Release with auto-generated notes
#    - Uploads archives as release assets
```

### Version Scheme

`0.MINOR.PATCH` — pre-1.0 (breaking changes may happen in minor bumps)

| Bump | When |
|------|------|
| PATCH (`0.2.0` → `0.2.1`) | Bug fixes, docs, CI changes |
| MINOR (`0.2.0` → `0.3.0`) | New phase features, breaking Studio/API changes |
| MAJOR (`0.x` → `1.0.0`) | Public API stability guarantee (post-MVP) |

### What Goes in Each Archive

```
duumbi-v{VERSION}-{TARGET}.tar.gz
├── duumbi              # CLI binary (compiler, mutation, intents, REPL, MCP)
├── studio              # Studio SSR web server (duumbi studio)
├── runtime/
│   └── duumbi_runtime.c  # C runtime (required for linking compiled binaries)
├── LICENSE
└── README.md
```

### Platforms

| Target | OS | Arch | Runner |
|--------|----|------|--------|
| `aarch64-apple-darwin` | macOS | ARM64 (M-series) | `macos-latest` |
| `x86_64-apple-darwin` | macOS | Intel | `macos-13` |
| `x86_64-unknown-linux-gnu` | Linux | x86_64 | `ubuntu-latest` |
| `aarch64-unknown-linux-gnu` | Linux | ARM64 | `ubuntu-latest` (cross) |

---

## Related

- [[DUUMBI - PRD]] — Product Requirements Document
- [[DUUMBI - Architecture Diagram]] — System architecture
- [[DUUMBI Roadmap Map]] — Phase roadmap
- [[DUUMBI - Phase 15 - Studio Workflow Redesign]] — v0.2.0 phase details
