---
tags:
  - project/duumbi
  - milestone/phase-15-studio
status: partial
github_milestone: "Phase 15: Studio Workflow Redesign"
github_issues: "9/13 closed, 4 open"
created: 2026-03-26
updated: 2026-03-29
---
# Phase 15 — Studio Workflow Redesign 🔄

> **Kill Criterion:** (1) A new user can go from `duumbi init` through intent → execute → build in CLI REPL in under 10 minutes using one of the 3 sample tasks. (2) The same 3 sample tasks complete successfully in Duumbi Studio with the simplified 3-panel workflow (Intents → Graph → Build). (3) The Studio chat panel sends real requests to the configured LLM and displays streaming responses in context of the visible graph.
> **Status:** 🔄 Partial — Core implementation merged ([PR #497](https://github.com/hgahub/duumbi/pull/497)); E2E kill criterion testing pending (#486–#488)
> **Branch:** ~~`phase15/studio-workflow-redesign`~~ (deleted — merged to `main`)

## Implementation Status

| Issue | Title | Status |
|-------|-------|--------|
| #477 | WebSocket chat endpoint | ✅ Closed |
| #478 | Context-aware chat prompt assembly | ✅ Closed |
| #479 | Live graph refresh after mutation | ✅ Closed |
| #480 | Footer redesign — 3 panels | ✅ Closed |
| #481 | Intents panel — unified create/review/execute | ✅ Closed |
| #482 | Graph panel — C4 drill-down + chat split view | ✅ Closed |
| #483 | Build panel — compile and run | ✅ Closed |
| #484 | Provider management in ⌘K | ✅ Closed |
| #485 | Agent templates in ⌘K | ✅ Closed |
| #486 | Sample task 1 — Calculator | ⏳ E2E testing |
| #487 | Sample task 2 — String Utilities | ⏳ E2E testing |
| #488 | Sample task 3 — Math Library | ⏳ E2E testing |
| #489 | E2E test protocol document | ⏳ Docs |

### PR #497 Highlights
- REPL terminal corruption fixes (bracketed paste, log buffer, no `eprintln!` in ratatui context)
- Braille spinner animation during LLM work (`⠋⠙⠹⠸⠼⠴⠦⠧⠇⠏`)
- WebSocket C4 context filtering (section-header approach)
- `collect_nodes_by_id` diff for changed node detection
- sha2 0.11 compatibility fix (byte iteration, not `LowerHex`)

← Back: [[DUUMBI Roadmap Map]]

---

## Design Decision: 3-Step Workflow, Not 8

Based on analysis of the existing codebase (Phase 1–12) and developer UX evaluation, the original 8-step Studio workflow (Settings → Agents → Intent describe → Intent review → Plan → Plans roadmap → Execute → Build) is replaced by a **3-panel workflow**:

### Intents (Describe → Plan → Review — one place)
The user writes an intent. The system auto-plans (coordinator decompose), the user reviews the task list inline, chats to refine, then clicks Execute. Settings and agent configuration are power-user features accessed via `⌘K` command palette, not primary navigation.

### Graph (C4 visualization + chat manipulation)
After execution, the user sees the C4 graph. The split-panel view (graph left, chat right) is the core Studio experience. The chat is context-aware: it knows which C4 level and which module the user is viewing. Manipulations via chat update the graph in real-time.

### Build (Compile → Run → Output)
One-click build. Shows compilation output, binary location, run result.

### Rationale
- The CLI REPL already proves that `intent create → execute → build` is the natural flow
- Agent/provider configuration is infrastructure — it belongs in `config.toml` and `⌘K`, not the main UI
- Plans/roadmap visualization adds value only for large multi-intent projects — premature for MVP
- The split-panel Graph view with context-aware chat is the actual differentiator vs CLI

---

## Tasks

### Track A: Studio Backend — Chat → LLM Integration

The Studio chat panel currently returns mock responses. This track connects it to the real mutation pipeline.

#### A1: Studio chat WebSocket endpoint
- Add `/ws/chat` WebSocket endpoint to the Studio axum server
- Message protocol: `{type: "chat", module: "app/main", c4_level: "component", message: "..."}`
- Server receives chat, builds context from the visible module/level, calls `mutate_streaming()`
- Streaming response chunks sent back over WebSocket as `{type: "chunk", text: "..."}`
- Final result sent as `{type: "result", ops_count: N, diff: "..."}`
- Error handling: validation failures sent as `{type: "error", diagnostics: [...]}`

#### A2: Context-aware chat
- The chat prompt includes the currently viewed module's JSON-LD source
- The C4 level determines context depth: Context level → module list only, Code level → full block ops
- Uses existing `context::assemble_context()` pipeline (Phase 10)
- Session history maintained per Studio session (in-memory, not persisted)

#### A3: Graph live update after mutation
- After successful mutation, the Studio re-fetches `/api/graph/{level}` and re-renders
- Client-side: WebSocket `result` message triggers graph data refresh
- Smooth transition: new nodes animate in, removed nodes fade out

### Track B: Studio Frontend — 3-Panel Navigation

Replace the 5-item footer navigation (Intents/Plans/Build/Agents/Registry) with the 3-panel workflow.

#### B1: Footer redesign — 3 panels
- Footer items: **Intents** (target icon) · **Graph** (grid icon) · **Build** (hammer icon)
- Remove Plans, Agents, Registry from footer
- Add them to `⌘K` command palette instead (already exists in mockup)

#### B2: Intents panel — unified create/review/execute
- Left sidebar: intent list (existing tree view)
- Main area: intent detail view with sections:
  - Description (editable textarea)
  - Acceptance criteria (editable list)
  - Task plan (auto-generated by coordinator, read-only with task status indicators)
  - Action buttons: **Save** (saves yaml), **Execute** (runs intent pipeline), **Delete**
- New intent: `+` button opens create form → LLM generates spec → user reviews → save
- Intent execution: progress shown inline (task-by-task with checkmarks/spinners)

#### B3: Graph panel — C4 + chat split view
- Reuse existing Leptos C4 drill-down from `crates/duumbi-studio/`
- Left: C4 graph visualization (Context → Container → Component → Code navigation)
- Right: Chat panel connected to real LLM (Track A)
- Provider connections are read from `config.toml`; Duumbi selects concrete models internally
- Graph refreshes after chat-initiated mutations

#### B4: Build panel — compile and run
- Single "Build" button → calls `build()` pipeline
- Output panel shows: compilation status, binary path, diagnostic errors
- "Run" button → executes binary, shows stdout/stderr
- Build errors link to relevant graph nodes (clickable → navigates to Graph panel)

### Track C: Settings via Command Palette

#### C1: Provider management in `⌘K`
- `⌘K` → "Settings: Configure providers" → opens overlay
- Shows current providers from `config.toml`
- Add/edit/remove provider (writes to `config.toml`)
- Test connection button (makes a minimal API call)

#### C2: Agent templates in `⌘K`
- `⌘K` → "Settings: Agent templates" → shows template list
- Read-only view of the 5 seed templates (Planner, Coder, Reviewer, Tester, Repair)
- Advanced: edit template system prompts (persists to `.duumbi/knowledge/agent-templates/`)

---

## Sample Tasks for End-to-End Testing

These 3 tasks serve as both development targets and kill criterion tests. Each must work in CLI REPL and Studio.

### Sample 1: Calculator (Simple — single module)
**Intent:** "Build a calculator with add, subtract, multiply, divide functions that work on i64 numbers"
- Expected: 1 module created (`calculator/ops`), main modified to demo, 4 test cases
- Agent profile: Simple + Create + SingleModule + Low → 1× Coder, Sequential
- Build output: binary that prints calculation results

### Sample 2: String Utilities (Moderate — multi-module)
**Intent:** "Create a string utility library with functions: reverse a string, count vowels, check if palindrome. Demo all three in main."
- Expected: 1 module created (`string/utils`), main modified, 3 test cases
- Agent profile: Moderate + Create + SingleModule → Planner + Coder + Tester, Pipeline
- Tests string ops from Phase 9a-1

### Sample 3: Math Library with Dependencies (Moderate — multi-module with cross-references)
**Intent:** "Build a math library with: factorial (recursive), fibonacci (iterative), and is_prime functions. The main function should compute factorial(10), fibonacci(15), and check if 97 is prime."
- Expected: 1 module (`math/lib`), main modified, 3 test cases
- Agent profile: Moderate + Create + SingleModule → Planner + Coder + Tester, Pipeline
- Tests recursion (Call), loops (Branch), and cross-module imports

---

## Walkthrough: CLI REPL

```bash
# Step 1: Create workspace
mkdir my-project && cd my-project
duumbi init

# Step 2: Launch REPL
duumbi
# (entering REPL — no arguments = interactive mode)

# Step 3: Create intent (Sample 1)
/intent create "Build a calculator with add, subtract, multiply, divide functions that work on i64 numbers"
# → LLM generates IntentSpec
# → Shows: acceptance criteria, modules (create: calculator/ops, modify: app/main), test cases
# → Prompts: "Save intent? [Y/n]"
y

# Step 4: Review (optional — intent is shown automatically after create)
/intent review calculator
# → Shows full YAML spec
# → User can /intent edit calculator to open in $EDITOR

# Step 5: Execute
/intent execute calculator
# → Plan (4 tasks):
#   [1/4] Create module calculator/ops with add, sub, mul, div
#   [2/4] Implement add function
#   [3/4] Implement remaining functions
#   [4/4] Modify main to demonstrate calculator
# → Each task streams LLM thinking, shows proposed changes
# → Verifier runs test cases
# → "Intent 'calculator' completed. 4/4 tasks, 4/4 tests passed."

# Step 6: Build and run
/build
# → "Build successful: .duumbi/build/output"
/run
# → Prints calculator results (e.g., "3 + 5 = 8", "10 / 2 = 5")

# Step 7: Explore the graph
/describe
# → Shows pseudocode of all functions

# Step 8: Iterate
/add "Add a power(base, exp) function to calculator/ops that computes base^exp"
# → Mutates the graph, validates, asks for confirmation
```

---

## Walkthrough: Duumbi Studio

```
# Step 1: Launch Studio (after duumbi init in the project directory)
duumbi studio
# → Opens browser at http://localhost:8421

# Step 2: Intents panel (default view)
# → Click "+" to create new intent
# → Type: "Build a calculator with add, subtract, multiply, divide functions that work on i64 numbers"
# → System generates spec (shows: criteria, modules, tasks)
# → Review the generated plan inline
# → Click "Execute"

# Step 3: Watch execution
# → Task list shows progress: ✓ Create module... ⟳ Implement add... ○ Remaining...
# → Each task's LLM reasoning streams in the right panel
# → On completion: "Intent completed — 4/4 tasks, 4/4 tests passed"

# Step 4: Navigate to Graph panel (click "Graph" in footer)
# → C4 Context level: shows the software system
# → Click to drill into Container → Component → Code
# → See all functions: add, sub, mul, div in calculator/ops module
# → Code level shows the semantic graph (JSON-LD ops)

# Step 5: Chat with the graph
# → In the chat panel (right side of Graph view):
# → Type: "Add a power(base, exp) function that computes base^exp"
# → LLM streams response, shows proposed changes
# → Graph updates live after confirmation

# Step 6: Build panel (click "Build" in footer)
# → Click "Build" button
# → Output: "Build successful: .duumbi/build/output"
# → Click "Run" → see output in terminal panel
```

---

## Architecture Changes

### Files to Create
```
crates/duumbi-studio/src/
├── ws.rs                    — WebSocket chat handler (A1)
├── components/
│   ├── intent_panel.rs      — Intents unified panel (B2)
│   ├── build_panel.rs       — Build panel (B4)
│   └── settings_overlay.rs  — Settings via ⌘K (C1, C2)
```

### Files to Modify
```
crates/duumbi-studio/src/
├── lib.rs              — Add WebSocket route, restructure layout
├── app.rs              — 3-panel navigation instead of 5
├── components/mod.rs   — Register new components
├── script/studio.js    — WebSocket client, live graph refresh
```

### No Changes Needed
```
src/agents/         — Phase 12 agent system works as-is
src/intent/         — Intent pipeline works as-is
src/context/        — Context assembly works as-is
src/mcp/            — MCP server works as-is
src/cli/commands.rs — Build pipeline works as-is
```

---

## Dependencies

```
Phase 5 (Intent)         ──→ This phase uses the intent pipeline
Phase 10 (Context)       ──→ Chat uses context assembly
Phase 12 (Agents + MCP)  ──→ Agent templates + MCP tools available
Phase 6 (Original Studio)──→ Leptos C4 visualization reused
Phase 11 (CLI UX)        ──→ REPL patterns inform Studio UX
```

---

## Non-Goals (Deferred)

- C4-hierarchical intent spec (the flat IntentSpec is sufficient for MVP)
- Plans/roadmap visualization (list view is sufficient)
- Registry search during intent creation (future enhancement)
- Language projection in code view (Phase 15b)
- Agent-to-model manual assignment UI (auto-assembly is better UX)
- Milestone grouping in task plans (flat task list is sufficient)

---

## Related

- [[DUUMBI - Phase 6 - DUUMBI Studio]] — Original Studio (Leptos C4 drill-down)
- [[DUUMBI - Phase 12 - Dynamic Agent System & MCP]] — Agent system
- [[DUUMBI - Phase 5 - Intent-Driven Development]] — Intent pipeline
- [[DUUMBI - Phase 10 - Intelligent Context & Knowledge Graph]] — Context assembly
- [[DUUMBI - Phase 11 - CLI UX & Developer Experience]] — REPL UX patterns
- [[DUUMBI Roadmap Map]] — Master roadmap
