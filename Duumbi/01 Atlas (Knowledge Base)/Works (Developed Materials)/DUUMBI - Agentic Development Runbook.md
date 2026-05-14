---
tags:
  - project/duumbi
  - doc/agent-workflow
  - doc/runbook
status: active
created: 2026-05-13
updated: 2026-05-13
related_maps:
  - "[[DUUMBI Agentic Development Map]]"
related_works:
  - "[[DUUMBI - Development Intake to Delivery Workflow]]"
---

# DUUMBI - Agentic Development Runbook

## Summary

This runbook is the user-facing operating guide for running the DUUMBI intake-to-delivery workflow with agents. The canonical process is defined in [[DUUMBI - Development Intake to Delivery Workflow]]. This document translates that process into practical run instructions: which stage runs next, who makes the decision, where to run the agent, which skill to use, and what prompt to give the agent.

The core rule is that GitHub is the execution source of truth. Obsidian stores durable workflow knowledge, but current issue status, PR state, checks, approvals, and closure evidence belong in GitHub Issues, GitHub Project, PRs, and CI.

## Visual Guide

![[DUUMBI - Agentic Development Runbook.png]]

## Pre-Flight Setup

Before running a delivery workflow, verify these conditions:

| Check | Required state | Where to verify |
|---|---|---|
| GitHub CLI auth | `repo`, `project`, and `read:org` scopes are available when using local `gh` | Codex local terminal with `gh auth status` |
| GitHub Project Status field | DUUMBI workflow statuses exist | GitHub Project `Duumbi project` |
| Workflow labels | `needs-human-review`, `accepted`, `needs-spec`, `product-spec-approved`, `needs-tech-spec`, `tech-spec-approved`, and review labels exist | `hgahub/duumbi` labels |
| Active workflow context | PRD, Glossary, Agentic Development Map, and workflow document are available | `duumbi-vault` |
| Source repo instructions | Repository `AGENTS.md` is available before source changes | target source repo, usually `duumbi` |
| Specs | Product and technical specs are linked before implementation | GitHub issue and spec PRs |

Use Codex App when a human is at the machine and wants guided local execution. Use Oz for cloud, scheduled, Slack-triggered, parallel, or long-running orchestration. Use Codex CLI or Claude Code CLI mainly when working directly in a terminal checkout and the operator is comfortable managing branches, commands, and evidence manually.

## Tool Selection Rules

| Tool | Best use | Avoid using it for |
|---|---|---|
| Codex App | Local vault work, GitHub workflow gates, spec drafting, implementation in new worktrees, PR review artifacts, closure coordination | Silent long-running unattended sweeps unless configured as an automation |
| Oz agent | Slack-triggered intake, scheduled sweeps, cloud runs, team-visible orchestration, parallel or long-running agent work | Final product decisions without explicit human approval |
| Codex CLI | Focused source work in a local checkout, quick terminal-native implementation or review runs | Durable Obsidian edits unless the operator handles vault conventions carefully |
| Claude Code CLI | Alternative local implementation or review agent when a second coding model is useful | Project-state mutation unless GitHub writes and evidence format are controlled |

Default model guidance for Codex App:

| Work type | Recommended reasoning |
|---|---|
| Intake, triage, acceptance brief, closure with clear evidence | GPT-5.5, medium |
| Product spec, technical spec, implementation coordination, review artifact | GPT-5.5, high |
| Cross-repo architecture or ambiguous technical planning | GPT-5.5, xhigh |
| Simple status updates or label/status-only routing | GPT-5.5, low or medium |

## End-To-End Run Table

| Stage | Skill | User or agent decision | Recommended repo | Run location | Best tool | Other viable tools | Output |
|---|---|---|---|---|---|---|---|
| 1 | `duumbi-obsidian-capture` | User asks Oz to capture Slack input | `duumbi-vault` | Cloud | Oz agent | Codex App if Slack context is copied in | Inbox note from Slack input |
| 2 | `duumbi-codex-intake` | User asks Codex to capture or refine an idea | `duumbi-vault` | Work locally | Codex App | Codex CLI | Inbox note from Codex input |
| 3 | `duumbi-github-intake` | Agent classifies existing GitHub item | `duumbi-vault` | Work locally | Codex App | Oz agent, Codex CLI | Stage 3 intake summary and Stage 4 recommendation |
| 4 | `duumbi-triage` | Agent routes item; human has not accepted yet | `duumbi-vault` | Work locally | Codex App | Oz agent | GitHub issue routed to `Needs Human Acceptance` |
| 5 | `duumbi-human-acceptance` | Human decides `Accept`, `Needs Clarification`, `Duplicate`, `Defer`, or `Reject` | `duumbi-vault` | Work locally | Codex App | Oz agent for Slack-carried approvals | Structured Stage 5 decision comment and next status |
| 6 | `duumbi-spec-draft` | Agent drafts product spec after acceptance | target source repo, usually `duumbi` | New worktree | Codex App | Codex CLI, Oz for cloud drafting | `specs/DUUMBI-<issue>/PRODUCT.md` draft PR or issue-comment spec |
| 7 | `duumbi-spec-review` | Human explicitly approves or requests changes | `duumbi-vault` | Work locally | Codex App | Oz agent | Stage 7 decision, status `Technical Spec Needed` when approved |
| 8 | `duumbi-tech-spec-draft` | Agent drafts implementation-facing technical spec | target source repo, usually `duumbi` | New worktree | Codex App | Codex CLI, Claude Code CLI for source analysis | `specs/DUUMBI-<issue>/TECHNICAL.md` draft PR |
| 9 | `duumbi-tech-spec-review` | Human explicitly approves or requests changes | `duumbi-vault` | Work locally | Codex App | Oz agent | Stage 9 decision, status `Ready for Build` when approved |
| 10a | `duumbi-implementation` | Agent prepares next implementation action | target source repo, usually `duumbi` | New worktree | Codex App | Codex CLI | Ralph Cycle approval request or PR evidence consolidation |
| 10b | `duumbi-ralph-cycle` | Human explicitly approves one bounded cycle | target source repo, usually `duumbi` | New worktree | Codex App | Codex CLI, Claude Code CLI for one bounded patch | One approved implementation cycle and evidence report |
| 11 | `duumbi-review-artifact` | Agent supports human merge decision | target source repo, usually `duumbi` | New worktree or Work locally | Codex App | Codex CLI, Claude Code CLI for independent review | Structured review artifact and merge-readiness recommendation |
| Human merge | none | Human merges or rejects PR | GitHub PR | GitHub UI or local git | Human | Codex CLI for merge commands if explicitly requested | Merged PR or returned-to-Stage-10 work |
| 12 | `duumbi-closure` | Agent verifies merged completion and closes loop | `duumbi-vault` | Work locally | Codex App | Oz agent for source-surface updates | Closure evidence, issue closure, Project `Done`, durable sync decision |
| 12 optional | `duumbi-knowledge-sync` when available, or `duumbi-closure` v1 sync path | Agent updates durable knowledge only if needed | `duumbi-vault` or target source repo | New worktree when editing files | Codex App | Codex CLI | Dot, Map, Work, skill, PRD, Glossary, or `AGENTS.md` update |

## Stage Prompts

Replace the issue, PR, and repository names before running. Keep prompts explicit about the stage boundary, because the skills intentionally refuse to cross gates.

### Stage 1 - Slack Intake

Use when the input starts in Slack.

```text
Run DUUMBI Stage 1 Slack intake with duumbi-obsidian-capture.

Source: <Slack thread URL or copied Slack context>
Goal: Capture the raw input into the DUUMBI Inbox, classify it, inspect active DUUMBI context, detect obvious duplicates, and report the next processing step.

Do not create GitHub issues, specs, or implementation changes.
```

### Stage 2 - Codex Intake

Use when the idea starts in Codex rather than Slack or GitHub.

```text
Run DUUMBI Stage 2 Codex intake with duumbi-codex-intake.

Input: <idea, bug, question, or source material>
Goal: Capture this into the DUUMBI Inbox in English, classify it, inspect relevant vault and GitHub context read-only, and recommend routing.

Do not create specs or implementation changes.
```

### Stage 3 - GitHub Intake

Use for an existing GitHub Issue or Ideas Discussion.

```text
Run DUUMBI Stage 3 GitHub intake with duumbi-github-intake.

Target: <GitHub issue or discussion URL>
Goal: Review the GitHub item, inspect active DUUMBI context, classify it, identify duplicates or clarification needs, and recommend Stage 4 routing.

Do not create issues, specs, Obsidian artifacts, PRs, source-code changes, or Project status changes.
```

### Stage 4 - Triage

Use when an item is ready to converge into the workflow.

```text
Run DUUMBI Stage 4 triage with duumbi-triage.

Target: <Inbox note path, GitHub issue URL, GitHub Discussion URL, or bounded source list>
Goal: Classify the source item, inspect relevant DUUMBI and GitHub context, update or create the execution artifact when appropriate, and route execution work to Needs Human Acceptance.

Do not create product specs, technical specs, PRs, source-code changes, or implementation branches.
```

If no target: Inbox note, issue or discussion:

```text
Run DUUMBI Stage 4 triage with duumbi-triage.

Target: bounded next-issue discovery sweep across Inbox notes, GitHub Issues, GitHub Ideas Discussions, and active DUUMBI Obsidian documentation.

Goal: Propose exactly one next best engineering issue.

Prioritize work that solves a concrete user, product, reliability, or developer-experience problem and is likely to deliver meaningful business value. Prefer work that fits current roadmap sequencing and avoids starting lower-priority phases before active kill-criterion work is closed.

Inspect:
- Inbox notes under `Duumbi/00 Inbox (ToProcess)/`
- GitHub Issues in intake, clarification, or `Todo` Project states
- GitHub Ideas Discussions
- active DUUMBI docs in duumbi-vault, especially PRD, Glossary, Agentic Development Map, Development Intake to Delivery Workflow, and current roadmap notes
- recent PRs, milestones, and directly relevant source files only as supporting evidence for duplicate risk, sequencing, feasibility, or whether work has already started

If a suitable issue already exists and its DUUMBI Project Status is `Todo`, recommend or update that canonical issue. Do not select issues that have already moved beyond `Todo`, including `Needs Human Acceptance`, `Spec Needed`, `Ready for Build`, `Cycle Authorization`, `In Progress`, or `In Review`; record them only as related context. If suitable work is not already represented by an eligible `Todo` issue, create one GitHub issue in hgahub/duumbi with full description, clear scope, evidence, acceptance criteria, risks, and relevant links. Connect it to the relevant milestone if one is clearly applicable.

Route the selected issue to Needs Human Acceptance.

Do not mark it accepted, do not create product specs, do not create technical specs, do not create PRs, do not modify source code, and do not start implementation.

Return:
- exactly one recommended issue number
- issue URL
- why this is the next best engineering issue
- evidence inspected
- assumptions and open questions
- GitHub writes performed

```
### Stage 5 - Human Acceptance

Use after triage, when a human has made an explicit decision.

```text
Run DUUMBI Stage 5 Human Acceptance with duumbi-human-acceptance.

Target issue: <GitHub issue URL>
Human decision: Accept
Reviewer source: <Codex | Oz | Slack | GitHub | other>
Rationale: <short human rationale>

Goal: Record the structured Stage 5 decision, set the issue to Spec Needed, add accepted and needs-spec labels, and remove needs-human-review when available.

Do not create product specs, technical specs, PRs, source-code changes, or implementation branches.
```

For non-acceptance decisions, replace `Accept` with `Needs Clarification`, `Duplicate`, `Defer`, or `Reject`, and include the rationale.

### Stage 6 - Product Spec Draft

Use after Stage 5 acceptance.

```text
Run DUUMBI Stage 6 Product Spec Draft with duumbi-spec-draft.

Target issue: <GitHub issue URL>
Expected artifact: specs/DUUMBI-<issue-number>/PRODUCT.md in <target source repo>, unless the skill determines that an issue-comment spec is sufficient.

Goal: Verify Stage 5 acceptance, inspect active DUUMBI context and relevant source context, draft the product spec in English, open a draft spec PR if file-based, link it from the issue, and move the issue to Spec Review.

Do not create technical specs, implementation code, or Ralph cycles.
```

### Stage 7 - Product Spec Review

Use after the product spec artifact exists and the human has reviewed it.

```text
Run DUUMBI Stage 7 Product Spec Review with duumbi-spec-review.

Target issue: <GitHub issue URL>
Product spec artifact: <PRODUCT.md PR URL or issue comment link>
Human decision: Approve
Reviewer source: <Codex | Oz | Slack | GitHub | other>
Rationale: Reviewed and approved the PRODUCT spec; it is clear enough to proceed to technical specification.

Goal: Record the structured Stage 7 decision, set the issue to Technical Spec Needed, remove needs-spec, and add product-spec-approved and needs-tech-spec.

Do not create a technical spec or implementation changes.
```

For changes, use `Human decision: Request Changes` and include blocking findings.

### Stage 8 - Technical Spec Draft

Use after product spec approval.

```text
Run DUUMBI Stage 8 Technical Spec Draft with duumbi-tech-spec-draft.

Target issue: <GitHub issue URL>
Product spec artifact: <PRODUCT.md PR URL or path>
Expected artifact: specs/DUUMBI-<issue-number>/TECHNICAL.md in <target source repo>

Goal: Verify product spec approval, inspect repo AGENTS.md and affected source areas, draft an agent-facing technical spec with bounded Ralph cycle instructions, open a draft PR, link it from the issue, and move the issue to Technical Spec Review.

Do not modify implementation code, tests, generated artifacts, runtime assets, or product specs.
```

### Stage 9 - Technical Spec Review

Use after the technical spec artifact exists and the human has reviewed it.

```text
Run DUUMBI Stage 9 Technical Spec Review with duumbi-tech-spec-review.

Target issue: <GitHub issue URL>
Technical spec artifact: <TECHNICAL.md PR URL or path>
Product spec artifact: <PRODUCT.md PR URL or path>
Human decision: Approve
Reviewer source: <Codex | Oz | Slack | GitHub | other>
Rationale: Reviewed and approved the technical specification; it is sufficiently bounded for Stage 10 Ralph-cycle implementation.

Goal: Record the structured Stage 9 decision, set the issue to Ready for Build, remove needs-tech-spec, and add tech-spec-approved.

Do not request a Ralph cycle or start implementation.
```

### Stage 10a - Implementation Coordination

Use first when the issue reaches `Ready for Build`.

```text
Run DUUMBI Stage 10 Implementation Coordination with duumbi-implementation.

Target issue: <GitHub issue URL>
Mode: prepare Ralph Cycle 1 approval request

Goal: Verify approved product and technical specs, inspect branch and PR state, identify the next bounded implementation goal, prepare a Ralph Cycle 1 Approval Request, set status to Cycle Authorization, and stop.

Do not modify implementation files. Do not run a Ralph cycle.
```

Use the same skill later for evidence consolidation:

```text
Run DUUMBI Stage 10 Implementation Coordination with duumbi-implementation.

Target issue: <GitHub issue URL>
Mode: consolidate PR evidence and move to In Review if completion criteria are met

Goal: Verify cycle evidence, branch, PR, product spec checks, and technical completion criteria. Update PR evidence or issue comments as needed, then move to In Review only if evidence is complete.

Do not edit implementation files and do not merge.
```

### Stage 10b - Ralph Cycle Approval

The human approval comment should be explicit.

```markdown
## Ralph Cycle <N> Approval

Approved.

Scope approved:
- Execute the proposed Ralph Cycle <N> only.
- Stay within <issue number and title> scope.
- Do not expand into related issues or documentation consolidation unless explicitly listed in the approved cycle.
- Stop after the planned checks and evidence report.

Reviewer source: GitHub
```

Then run the approved cycle:

```text
Run DUUMBI Stage 10 Ralph Cycle with duumbi-ralph-cycle.

Target issue: <GitHub issue URL>
Mode: execute approved Ralph Cycle <N>

Goal: Execute exactly the approved Cycle <N> scope, set status to In Progress when available, make only the approved implementation changes, run the planned checks, write the evidence report, and stop.

Do not start Cycle <N+1>. If work remains, prepare the next approval request and return the issue to Cycle Authorization.
```

### Stage 11 - Review Artifact

Use when implementation evidence is ready and the PR is in review.

```text
Run DUUMBI Stage 11 Review Artifact with duumbi-review-artifact.

Target PR: <implementation PR URL>
Linked issue: <GitHub issue URL>
Product spec: <PRODUCT.md artifact>
Technical spec: <TECHNICAL.md artifact>

Goal: Review the PR against the approved product spec, technical spec, CI/checks, changed files, and Ralph cycle evidence. Write a structured review artifact and recommend whether it is ready for a human merge decision.

Do not merge, close the issue, move the Project item to Done, perform closure, or edit implementation code.
```

### Human Merge Decision

After Stage 11 recommends readiness, the human reviewer merges or sends the PR back to Stage 10.

```text
Human action: Merge <PR URL> after reviewing Stage 11 evidence, CI/checks, and remaining risks.

If merge is not acceptable, comment with required changes and route the issue back to Stage 10.
```

### Stage 12 - Closure

Use only after merge or equivalent completion evidence exists.

```text
Run DUUMBI Stage 12 Closure with duumbi-closure.

Merged PR or completion evidence: <merged PR URL>
Linked issue: <GitHub issue URL>

Goal: Verify merge evidence, Stage 11 review artifact, approved product spec, approved technical spec, checks, and final human decision. Add closure evidence to the issue, set Project status to Done when available, close the issue when appropriate, update source surfaces when available, process related Inbox notes if any, and decide whether durable knowledge sync is needed.

Do not merge PRs, start implementation, rewrite specs, or copy PR summaries into Obsidian unless there is durable reusable learning.
```

### Optional Durable Knowledge Sync

Run this only when Stage 12 says durable learning is needed or blocked.

```text
Run DUUMBI durable knowledge sync for the completed work.

Completion evidence: <merged PR URL>
Linked issue: <GitHub issue URL>
Closure evidence: <Stage 12 closure comment or report>
Target knowledge area: <Dot | Map | Work | skill | PRD | Glossary | source repo AGENTS.md>

Goal: Update only reusable durable guidance that changes future behavior, architecture, product understanding, workflow, vocabulary, or agent instructions. Keep facts, decisions, assumptions, recommendations, and open questions separate, and link back to the issue, PR, specs, and review evidence.

Do not mirror live GitHub status and do not copy the PR summary into Obsidian.
```

## Human Decision Points

| Decision point | Human must decide | Valid decisions |
|---|---|---|
| Stage 5 | Whether triaged work deserves specification effort | `Accept`, `Needs Clarification`, `Duplicate`, `Defer`, `Reject` |
| Stage 7 | Whether PRODUCT spec is approved | `Approve`, `Request Changes`, `Needs Clarification`, `Reject / No Longer Needed` |
| Stage 9 | Whether TECHNICAL spec is approved for implementation | `Approve`, `Request Changes`, `Needs Clarification`, `Reject / No Longer Needed` |
| Stage 10 | Whether each Ralph cycle is approved | `Approve this cycle`, request narrower cycle, or block |
| Stage 11 | Whether PR can merge | merge, request changes, block, or clarify |
| Stage 12 | Whether durable knowledge sync is needed | sync performed, sync not needed, or sync blocked |

Agents may recommend decisions, but they must not invent human acceptance.

## Repository And Location Defaults

| Work type | Default repo | Default location | Reason |
|---|---|---|---|
| Intake, triage, acceptance, review gates, closure | `duumbi-vault` | Work locally | Needs active vault context and GitHub state, usually no source edits |
| Product spec draft | target source repo, usually `duumbi` | New worktree | Creates `specs/DUUMBI-<issue>/PRODUCT.md` and a draft PR |
| Technical spec draft | target source repo, usually `duumbi` | New worktree | Creates `specs/DUUMBI-<issue>/TECHNICAL.md` and a draft PR |
| Implementation coordination | target source repo, usually `duumbi` | New worktree | Needs branch and PR context but should not edit files before cycle approval |
| Ralph cycle implementation | target source repo, usually `duumbi` | New worktree | Makes bounded implementation edits and runs checks |
| Review artifact | target source repo, usually `duumbi` | New worktree or Work locally | Needs PR diff, checks, specs, and cycle evidence |
| Closure | `duumbi-vault` | Work locally | Verifies GitHub completion and decides durable knowledge sync |
| Durable knowledge sync | `duumbi-vault` for Obsidian, target source repo for `AGENTS.md` | New worktree when editing files | Keeps durable knowledge and repo-local agent rules versioned |

## Common Mistakes

- Do not treat `Todo` as `Needs Human Acceptance`; they are different workflow states.
- Do not move from `Spec Review` to implementation. Stage 7 approval must be followed by Stage 8 and Stage 9.
- Do not treat issue acceptance as Ralph cycle approval. Each implementation cycle needs its own explicit approval.
- Do not run `duumbi-ralph-cycle` repeatedly in one prompt. One approval equals one cycle.
- Do not merge or close from Stage 11. Stage 11 supports the human merge decision; Stage 12 closes after merge.
- Do not sync every PR summary into Obsidian. Sync only reusable durable knowledge.

## Sources

- [[DUUMBI - Development Intake to Delivery Workflow]]
- [[DUUMBI Agentic Development Map]]
- [[Warp Oz and Codex Development Toolchain]]
- [[Agent Skills as Operational Playbooks]]
- [[How to use]]
