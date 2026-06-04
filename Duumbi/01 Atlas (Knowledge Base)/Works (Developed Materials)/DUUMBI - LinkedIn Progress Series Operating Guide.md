---
tags:
  - project/duumbi
  - doc/marketing
  - doc/runbook
  - surface/linkedin
status: active
created: 2026-06-04
updated: 2026-06-04
related_works:
  - "[[DUUMBI - Agentic Development Runbook]]"
---

# DUUMBI - LinkedIn Progress Series Operating Guide

## Summary

The DUUMBI LinkedIn progress series calendar is a pre-publication editorial operating plan for showing development progress in public. It is not a product roadmap, scheduler, publishing automation, or source of truth for implementation state.

Source package:

- Calendar: https://github.com/hgahub/duumbi-web/blob/main/content/social/linkedin-progress-series/calendar.md
- Evidence map: https://github.com/hgahub/duumbi-web/blob/main/content/social/linkedin-progress-series/evidence-map.md
- Hashtag strategy: https://github.com/hgahub/duumbi-web/blob/main/content/social/linkedin-progress-series/hashtag-strategy.md
- Visual templates: https://github.com/hgahub/duumbi-web/blob/main/content/social/linkedin-progress-series/visual-templates.md
- LinkedIn drafts: https://github.com/hgahub/duumbi-web/tree/main/content/social/linkedin-progress-series/posts
- Twitter/X variants: https://github.com/hgahub/duumbi-web/tree/main/content/social/linkedin-progress-series/x

Delivery issue:

- https://github.com/hgahub/duumbi/issues/370

Implementation PRs:

- https://github.com/hgahub/duumbi-web/pull/3
- https://github.com/hgahub/duumbi-web/pull/4

## How To Use The Calendar

1. Open `calendar.md` and select the current weekly entry.
2. Check the weekly `Status` field.
3. If the week is `Drafted`, use the paired LinkedIn and Twitter/X drafts as the starting point.
4. If the week is `Planned` or `needs source refresh`, do not publish it unchanged.
5. Re-verify every technical claim against `evidence-map.md`, the DUUMBI repository, public docs, public blog posts, and linked issues or PRs.
6. Use `visual-templates.md` as the design brief for the weekly image or graphic.
7. Use `hashtag-strategy.md` to choose the baseline and rotation hashtags.
8. Perform a final editorial check for unsupported claims, private material, unpublished roadmap promises, credentials, customer references, and accidental scheduling or publishing language.
9. Publish manually only after the evidence and editorial checks pass.

## Week Status Rules

| Status | Meaning | Required action before publication |
|---|---|---|
| `Drafted` | The post has a prepared draft and cross-post variant. | Refresh evidence, edit for current facts, then publish manually if still accurate. |
| `Planned` | The post is only a brief. | Draft the full post, validate sources, and create or update the paired Twitter/X variant. |
| `needs source refresh` | The topic depends on current repo or docs behavior. | Inspect current source/docs before writing or publishing. |
| `depends on prior weeks` | The post must summarize earlier outputs. | Review prior published posts and current public evidence first. |

## Weekly Operator Checklist

- Identify the target week and theme.
- Confirm that the evidence source still exists and supports the claim.
- Replace stale claims with current public facts.
- Keep future-facing claims explicitly labeled as planned.
- Avoid claims about production adoption, enterprise readiness, benchmarks, users, revenue, partners, or customers unless a public source is added.
- Confirm the visual template is defined and appropriate for the week.
- Confirm hashtags include the required baseline set when appropriate: `#rust`, `#compiler`, `#ai`, and `#semanticweb`.
- Keep publishing manual. Do not add social account access, scheduler configuration, social API integration, credentials, or generated binary assets without a separate approved issue.

## Recommended Automation

Use a scheduled GitHub Action as the primary automation, with Slack as the notification surface.

Recommended shape:

- Repository: `hgahub/duumbi-web`, because the calendar package lives there.
- Trigger: weekly `schedule` plus manual `workflow_dispatch`.
- Inputs for manual dispatch: `week_number`, optional `dry_run`.
- Behavior:
  - Read `content/social/linkedin-progress-series/calendar.md`.
  - Select the next unpublished or requested week.
  - Read the matching draft files when they exist.
  - Build a deterministic task brief with links to the calendar entry, evidence map, draft, visual template, and hashtag strategy.
  - Post the task brief to Slack.
  - Optionally open or update a GitHub issue in `hgahub/duumbi` when the week needs source refresh or drafting work.
  - Include a ready-to-run Codex prompt, but do not call a model directly from the Action.

This matches the DUUMBI operating model: GitHub Actions perform deterministic scheduling and notifications; Codex App, Codex Cloud, or a human editor performs the generative review and editing work.

## Why Not ChatGPT Pulse As The Primary Automation

ChatGPT Pulse can be useful as a personal reminder, but it should not be the system of record for this workflow.

Use Pulse only as a secondary reminder if desired:

- "Every Monday, remind me to review the next DUUMBI LinkedIn progress-series week."

Do not rely on Pulse to track publication state, inspect GitHub files, update issue evidence, or coordinate Slack/GitHub workflow state. Those belong in GitHub and Slack.

## Why Not A Fully Automated Publisher

Do not automate direct LinkedIn or Twitter/X publishing yet.

Reasons:

- Weeks 5-12 require source refresh before publication.
- Social account credentials would introduce avoidable risk.
- The current package is review-only by design.
- Manual publication keeps public claims accountable to current evidence.

## Suggested Weekly Slack Brief

```text
DUUMBI LinkedIn progress-series reminder

Target week: Week <N> - <theme>
Calendar: <calendar link>
LinkedIn draft: <draft link or "not drafted yet">
Twitter/X variant: <variant link or "not drafted yet">
Evidence map: <evidence-map link>
Visual template: <visual-template link>
Hashtag strategy: <hashtag-strategy link>

Required checks:
- Refresh source evidence.
- Remove or label future claims.
- Confirm no private material or unsupported adoption claims.
- Prepare final LinkedIn and Twitter/X copy.
- Publish manually only after review.

Suggested Codex prompt:
Review DUUMBI LinkedIn progress-series Week <N>. Verify current public evidence, update the LinkedIn and Twitter/X drafts if needed, keep future claims labeled, and report a publication-ready brief. Do not publish, schedule, add credentials, or modify runtime code.
```

## Durable Decision

Recommended automation: scheduled GitHub Action plus Slack notification, with optional issue creation for weeks that need source refresh or drafting. ChatGPT Pulse is acceptable only as a personal secondary reminder.
