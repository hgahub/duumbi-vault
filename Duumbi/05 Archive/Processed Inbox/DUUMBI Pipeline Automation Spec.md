---
tags:
  - project/duumbi
  - doc/automation-spec
  - stage/inbox
status: draft
created: 2026-05-22
updated: 2026-05-22
related_maps:
  - "[[DUUMBI - Agentic Development Runbook]]"
  - "[[DUUMBI Agentic Development Map]]"
related_works:
  - "[[DUUMBI - Agentic Development Runbook]]"
---

# DUUMBI — Pipeline Automation Specification

## Status

**Draft** — pending review and acceptance decision.

## Summary

Reduce developer touchpoints to decisions + one-click Codex launches. All heavy generative work (spec drafting, implementation, review) runs in Codex on the flat-rate OpenAI Pro subscription. All deterministic routing, labeling, project updates, and Slack notifications run in GitHub Actions at near-zero cost. Oz is used only for light cloud work (intake capture, triage sweep).

## Problem statement

The DUUMBI 12-stage workflow currently requires the developer to:
1. Manually track which stage each issue is in
2. Open Codex, locate the right skill prompt in the runbook, find issue/spec URLs, paste and execute
3. Monitor Codex for completion and manually advance to the next stage

This makes the developer the orchestration layer, creating friction at every handoff and burning Warp credits for expensive generative work that Codex could handle for free.

## Cost model

| Execution layer | Cost | Best for |
|---|---|---|
| GitHub Actions | ~$0 (free tier) | Deterministic: routing, labels, project updates, Slack notifications |
| Slack bridge (Azure Function) | ~$0/month (consumption) | Button → dispatch |
| Codex (OpenAI Pro) | $0 marginal (flat-rate) | Heavy generative: spec drafting, implementation, review artifact |
| Oz (Warp credits) | ~5–200 credits/turn | Light cloud work: intake, triage, closure decisions |

**Target:** ~20–65 Oz credits per ticket vs. ~400–1100+ for an all-Oz approach.

## Architecture decisions

1. **Codex-first for heavy stages** — Spec drafting (S6, S8), implementation (S10), and review artifact (S11) run in Codex. GitHub Actions provide ready-to-run prompts.
2. **GitHub Actions for deterministic routing** — Label changes trigger Slack cards; `stage-approval.yml` posts ready-to-run prompts to Slack.
3. **Oz only for light work** — Slack-triggered intake (S1), triage sweep (S4), and closure sync decision (S12 partial).
4. **No auto-chain for Codex stages** — After approval, Slack shows a pre-filled Codex prompt. Developer clicks to launch. One-click, not copy-paste.
5. **No auto-merge** — Human merge gate is preserved as the last safety net.

## New and modified workflows

### 1. `stage-approval.yml` — Ready-to-run prompt extension (MODIFY)

**What:** After recording an approval decision (stages 5, 7, 9), `stage-approval.yml` adds a Slack block with the pre-filled Codex prompt for the next stage.

**Why:** Eliminates the developer's lookup work (find runbook → find prompt → find issue URL → find spec URL → fill placeholders). Becomes: see Slack card → copy prompt → paste into Codex.

**Slack block to add to the existing approval result message:**

```
## Next step — Stage <N>

Run DUUMBI Stage <stage_number> <skill_name> with duumbi-<skill-name>.

Target issue: <issue_url>
<Product/Tech> spec artifact: <artifact_url>

Goal: <one-line goal from stage prompt>

Do not create <other_stage> specs, implementation changes, or unrelated PRs.
```

**Stages that generate Codex ready-to-run prompts:**
- Stage 5 approval → Stage 6 product spec draft
- Stage 7 approval → Stage 8 tech spec draft
- Stage 9 approval → Stage 10 implementation

**Implementation:**
In `stage-approval.yml`, add a `notify_slack_codex_prompt` job that runs after `execute` in all approval paths. It reads `stage`, `issue_number`, and `decision` from job outputs, fetches spec artifact URLs from existing comments (already done in `execute`), and posts to Slack with the prompt block.

**Slack block example (Stage 5 → Stage 6):**

```
## Next step — Stage 6

Run DUUMBI Stage 6 Product Spec Draft with duumbi-spec-draft.

Target issue: https://github.com/hgahub/duumbi/issues/123
Product spec artifact: https://github.com/hgahub/duumbi/pull/456

Goal: Verify Stage 5 acceptance, inspect active DUUMBI context and relevant source context, draft the product spec in English with BDD Scenarios, open a draft spec PR if file-based, link it from the issue, and move the issue to Spec Review.

Do not create technical specs, implementation code, or Ralph cycles.

---
⚠️ Spec-only PR rule: do not use "Closes", "Fixes", or "Resolves" for the execution issue. Use "Related to #<issue>" instead.
```

**Same pattern for Stage 7 → Stage 8 and Stage 9 → Stage 10**, substituting the appropriate stage number, skill name, goal text, and do-not-create exclusions.

**Label auto-addition note:** Stages 6 and 8 skills must add `spec-review` and `technical-spec-review` labels respectively upon completion. These labels already trigger the existing notification workflows.

---

### 2. `post-merge-closure.yml` — Deterministic closure (NEW)

**What:** Triggered on `pull_request: closed` (merged == true). Finds the linked issue, runs deterministic closure steps, posts a Slack card asking whether knowledge sync is needed.

**Why:** Stage 12 closure is currently manual. The mechanical parts (comment, label, project, close) are 100% deterministic — no LLM needed. Only the "knowledge sync needed?" decision requires judgment.

**Triggers:**
- `pull_request` event with `action: closed` and `merged: true`
- `workflow_dispatch` for manual runs

**Jobs:**

1. `find_issue` — Extract linked issue number from PR body (`Related to #N`, `Supports #N`, or PR description). Fallback: search for the most recent open issue with matching PR, or prompt via Slack for manual link.

2. `closure` — Run deterministic steps:
   - Post closure comment to issue (templates: `## Stage 12 Closure\n**Decision:** Merged\n**PR:** <PR_URL>\n**Review date:** <today>\n**Knowledge sync:** pending`)
   - Add `done` label
   - Remove `in-review`, `needs-review` labels if present
   - Update Project V2 status to `Done` (GraphQL, same pattern as `stage-approval.yml`)
   - Close issue (only if no open children)

3. `notify_slack` — Post a Slack card:
   ```
   ✅ PR #<N> merged for issue #<M>

   Closure steps completed:
   - Decision comment posted
   - Labels updated
   - Project → Done
   - Issue closed

   Knowledge sync needed? (Sync durable learning to vault/AGENTS.md)
   [ Yes — Run Codex ]  [ No — Done ]
   ```
   The "Yes" button dispatches `stage-launch` (see #5 below) with `{ stage: "12-sync", issue_number: N }`.

**Files changed:** `.github/workflows/post-merge-closure.yml`

---

### 3. `ralph-cycle-approval-request.yml` — Resource gate notification (NEW)

**What:** Slack notification card when a Ralph cycle stops and requests resource approval before continuing.

**Why:** Stage 10 resource gates currently have no notification — the developer only knows if actively monitoring Codex.

**Triggers:**
- `repository_dispatch: ralph-cycle-approval-request`
- `workflow_dispatch` (manual, with `issue_number` input)
- Label: `needs-cycle-approval` (applied by `duumbi-ralph-cycle` when gate triggers)

**Jobs:**

1. `validate_and_build` — Fetch issue title, labels, cycle resource estimate from a `Ralph Cycle <N> Resource Approval Request` comment already posted by the agent. Extract: cycle number, estimated LLM cost ($ and call count), proposed next scope, blocker summary.

2. `notify_slack` — Post card:
   ```
   ⏸ Stage 10 — Ralph Cycle <N> resource approval needed
   Issue: <issue_url>
   Estimated cost: $X / Y calls
   Proposed scope: <scope summary>
   Blocker: <if any>

   [ ✅ Approve Cycle ]  [ ↩️ Narrow Scope ]  [ ❌ Reject / Defer ]
   ```
   Approval/Rejection buttons route to `stage-approval.yml` (stage=10) via `repository_dispatch`. "Narrow Scope" requests clarification.

   GitHub Actions fallback link included in the card.

**Files changed:** `.github/workflows/ralph-cycle-approval-request.yml`

---

### 4. `slack-approval-bridge` — Extend for `stage-launch` dispatch (MODIFY)

**What:** Extend the Azure Function to handle a new action type: `stage_launch`.

**Why:** The "Yes — Run Codex" button in the post-merge closure Slack card needs to route somewhere. Instead of spawning an Oz agent, it should post the ready-to-run Codex prompt to the Slack thread so the developer can launch Codex with one click.

**New action type in `slackApproval.js`:**

```javascript
// In the handler, after parsing actionData:
const actionType = actionData.action_type; // "stage_approval" | "stage_launch"

// Route to appropriate handler
if (actionType === 'stage_launch') {
  await handleStageLaunch(actionData, responseUrl, context);
}
```

**`handleStageLaunch` behavior:**
1. Fetch the skill name and stage from `actionData.stage` and `actionData.skill`.
2. Fetch the linked issue number from `actionData.issue_number`.
3. Fetch spec artifact URLs from existing comments on the issue (Stage 6/8 comment → product/tech spec URL).
4. Build the ready-to-run Codex prompt (same template used in `stage-approval.yml`).
5. Post to Slack: `🚀 Ready to run Stage <N> — click below to launch in Codex` with the full prompt block.
6. Do NOT dispatch to Oz (that would burn credits for Codex-eligible work).

**Alternative (if "Yes" should trigger Oz directly):** Call the Oz API to start a cloud agent with the skill spec. But given the cost model, Slack → developer → Codex is preferred.

**Files changed:** `scripts/slack-approval-bridge/src/functions/slackApproval.js`

---

### 5. `stage-launch.yml` — Optional Oz dispatch for light stages (NEW)

**What:** GitHub Action triggered by `repository_dispatch: stage-launch`. Calls the Oz API to start a cloud agent with a specified skill.

**Why:** For light stages (S1 intake, S4 triage sweep, S12 knowledge sync), an Oz cloud agent is appropriate. This workflow provides the Oz API integration point.

**Note:** This workflow is **optional** — most stages launch Codex prompts instead of Oz agents. This exists for the light stages where cloud execution is genuinely better than Codex.

**Triggers:**
- `repository_dispatch: stage-launch`
- `workflow_dispatch` (for testing)

**Inputs:**
```yaml
stage: "1" | "4" | "12-sync"
issue_number: 123
skill: "duumbi-obsidian-capture" | "duumbi-triage" | "duumbi-knowledge-sync"
prompt_supplement: ""  # optional additional context
```

**Jobs:**

1. `dispatch_oz` — Uses Warp Oz API (via `oz agent run-cloud` CLI or SDK) to start a cloud agent:
   - `environment_id`: `eKLEWjD4PNqFC6j0EcDEYA` (existing duumbi-vault-knowledge-env)
   - `skill_spec`: `{owner}/duumbi:.agents/skills/{skill}/SKILL.md` or `hgahub/duumbi:.agents/skills/{skill}/SKILL.md`
   - `prompt`: constructed from stage prompt template + issue context + `prompt_supplement`
   - `name`: `duumbi-s{stage}-{issue_number}` for traceability

2. `notify_slack` — After dispatch, post confirmation:
   ```
   🚀 Oz agent launched for Stage <N> ({skill})
   Issue: #<M>
   Run: <session_link>
   ```

**Secrets required:** `OZ_API_KEY` (Warp API key)

**Files changed:** `.github/workflows/stage-launch.yml`

---

### 6. `slack-approval-bridge-deploy.yml` — Auto-deploy (NEW)

**What:** GitHub Action that deploys the Slack bridge Azure Function when `scripts/slack-approval-bridge/` changes on `main`.

**Why:** Manual `func azure functionapp publish` is error-prone. Auto-deploy ensures the bridge is always in sync with the latest code.

**Triggers:**
- Push to `main` with changes in `scripts/slack-approval-bridge/**`
- `workflow_dispatch` for manual deploy

**Jobs:**

1. `validate` — Check that `host.json`, `package.json`, and `src/functions/*.js` are all present.

2. `deploy` — Run `func azure functionapp publish func-duumbi-slack-bridge` with `--javascript` runtime flag.
   - Requires Azure credentials (`AZURE_SUBSCRIPTION`, `AZURE_RG`, etc.) via `azure/login@v2`.
   - Requires `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` secret (publishing profile from Azure portal).

3. `verify` — Health check: `curl https://func-duumbi-slack-bridge.azurewebsites.net/api/slack-approval -X POST -d "{}"` with expected 401 response (valid signature check fails, but endpoint is up).

**Files changed:** `.github/workflows/slack-approval-bridge-deploy.yml`

---

### 7. `duumbi-knowledge-sync` skill (NEW)

**What:** Extract from `duumbi-closure` into a standalone skill. Runs in Codex after Stage 12 decides sync is needed.

**Why:** `duumbi-closure` currently handles sync within its own execution. Extracting it into a dedicated skill makes it reusable, independently testable, and keeps `duumbi-closure` focused on deterministic closure.

**Trigger:** Developer clicks "Yes — Run Codex" in the post-merge closure Slack card (or manually runs in Codex).

**Inputs:**
- `completion_evidence`: merged PR URL
- `linked_issue`: GitHub issue URL
- `closure_evidence`: Stage 12 closure comment URL
- `target_knowledge_area`: `Dot | Map | Work | skill | PRD | Glossary | source repo AGENTS.md`

**Outputs:**
- Updates only reusable durable guidance (workflow improvements, architecture decisions, agent rules)
- Links back to issue, PR, specs, and review evidence
- Does NOT mirror live GitHub state or copy PR summaries into Obsidian

**Skill file:** `.agents/skills/duumbi-knowledge-sync/SKILL.md`

---

### 8. Skill label auto-addition enforcement (MODIFY)

**What:** All skills that complete and route to a review stage must reliably add the appropriate trigger label.

**Required label additions:**
- `duumbi-spec-draft` (Stage 6) → adds `spec-review` label on completion
- `duumbi-tech-spec-draft` (Stage 8) → adds `technical-spec-review` label on completion
- `duumbi-ralph-cycle` (Stage 10) → adds `needs-cycle-approval` label when resource gate triggers
- `duumbi-review-artifact` (Stage 11) → adds `needs-review` or equivalent label when PR is ready

**Verification:** Add a comment to the skill SKILL.md files noting the required label behavior. The existing notification workflows (`spec-review-request.yml`, `technical-spec-review-request.yml`) depend on these labels.

---

## Implementation order (priority)

| # | Change | Type | Cost | Impact |
|---|---|---|---|---|
| 1 | `stage-approval.yml` — ready-to-run prompt block | Modify | Free (GHA) | High — eliminates daily lookup friction |
| 2 | `post-merge-closure.yml` | New | Free (GHA) | High — closes the biggest manual gap |
| 3 | `slack-approval-bridge` — extend for `stage_launch` | Modify | Free (Azure) | Medium — enables Slack → Codex flow |
| 4 | `ralph-cycle-approval-request.yml` | New | Free (GHA) | Medium — fills the last missing gate |
| 5 | Skill label auto-addition enforcement | Modify | Free | Medium — ensures notification workflows fire |
| 6 | `stage-launch.yml` (Oz dispatch for light stages) | New | Low (Oz credits) | Low — S1/S4/S12 only |
| 7 | `slack-approval-bridge-deploy.yml` | New | Free (GHA) | Low — operational hygiene |
| 8 | `duumbi-knowledge-sync` skill | New | Free (Codex) | Low — nice-to-have |

---

## Out of scope

- **Periodic triage sweep** — developer manages Inbox directly; no auto-trigger
- **Auto-merge** — human merge gate is intentional
- **Stage 11 review artifact auto-trigger via Oz** — Slack notification with Codex prompt preferred over Oz for this stage
- **Auto-chain from spec completion to next stage** — ready-to-run prompt is human-triggered, not auto-run

---

## References

- [[DUUMBI - Agentic Development Runbook]] — stage prompts, tool selection rules, spec artifact requirements
- [[DUUMBI Agentic Development Map]] — workflow visualization
- Existing workflows: `ci.yml`, `coverage.yml`, `stage-approval.yml`, `human-acceptance-request.yml`, `spec-review-request.yml`, `technical-spec-review-request.yml`
- Slack bridge: `scripts/slack-approval-bridge/src/functions/slackApproval.js`

## Triage result
- Date: 2026-05-22
- Classification: execution work
- Routing: split into four GitHub execution issues and routed to Needs Human Acceptance.
- GitHub artifacts:
  - https://github.com/hgahub/duumbi/issues/593
  - https://github.com/hgahub/duumbi/issues/594
  - https://github.com/hgahub/duumbi/issues/595
  - https://github.com/hgahub/duumbi/issues/596
- Obsidian artifacts: none created; this note was sufficient source material and GitHub now owns execution state.
- Canonical duplicate: none found during GitHub duplicate search.
- Open questions:
  - Should generated next-stage prompts be Slack-only or also appended to GitHub decision comments?
  - Should missing workflow labels such as `needs-cycle-approval`, `needs-review`, and `done` be created as first-class labels?
  - Should Stage 10 resource decisions live in `stage-approval.yml` or a dedicated Stage 10 authorization workflow?
  - Is the Oz API/CLI contract stable enough for GitHub Actions, and which Azure deployment credential path should be used?
- Assumptions:
  - Heavy generative stages should remain Codex-prompted rather than Oz-dispatched by default.
  - Post-merge closure must conservatively skip ambiguous issue links and spec-only PRs.
  - Optional cloud/deployment work should follow the core approval-prompt and closure automation rather than block it.
- Next stage: Stage 5 Human Acceptance for issues #593, #594, #595, and #596.
