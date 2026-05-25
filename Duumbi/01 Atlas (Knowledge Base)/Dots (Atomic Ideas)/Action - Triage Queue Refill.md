---
type: dot
topic: github-actions
status: active
---

# Action - Triage Queue Refill

## Short Description

`Triage Queue Refill` is the scheduled Stage 4 refill automation for the DUUMBI
human acceptance queue. Its job is to keep enough candidate issues waiting in
GitHub Project V2 with `Status = Needs Human Acceptance` so the human Stage 5
gate has work ready to review.

The Action does not count `Todo` issues. It counts open Project V2 items whose
Status is exactly `Needs Human Acceptance`.

If at least three open issues are already waiting in `Needs Human Acceptance`,
the Action exits quietly. It does not call the model, does not mutate GitHub,
and does not send Slack.

If fewer than three issues are waiting, the Action performs one bounded refill:
it asks the triage model to choose at most one next candidate, then either routes
one existing eligible `Todo` issue or creates one new GitHub Issue and moves it
to `Needs Human Acceptance`.

## Skill Used

The Action implements the scheduled automation form of:

- `.agents/skills/duumbi-triage`

Conceptually, it uses the Stage 4 triage rules:

- inspect active Inbox notes, GitHub Issues, Ideas Discussions, and relevant
  Atlas/runbook context;
- deduplicate against active GitHub and vault context;
- choose execution work only when it can be routed safely;
- route execution work to `Needs Human Acceptance`;
- stop before product spec, technical spec, implementation, or human acceptance
  decisions.

The workflow itself runs through:

- `.github/workflows/triage-queue-refill.yml`
- `scripts/github-actions/triage-queue-refill.mjs`

## Triggers

The Action is triggered in two ways:

- Scheduled: every four hours with cron `0 */4 * * *`.
- Manual: `workflow_dispatch`, optionally overriding
  `target_human_acceptance_min`.

The default target minimum is `3` open Project V2 issues in
`Needs Human Acceptance`.

## Refill Behavior

On each run, the Action:

1. Checks out the source repository.
2. Attempts to check out `duumbi-vault` for bounded planning context.
3. Reads the configured GitHub Project V2 using `GH_PROJECT_PAT`.
4. Counts open issues in `Status = Needs Human Acceptance`.
5. Exits quietly if the count is at or above the target minimum.
6. If the count is below the target, calls DeepSeek with bounded triage context.
7. Accepts only strict JSON decisions:
   - `route_existing_issue`
   - `create_issue`
   - `needs_clarification`
   - `no_action`
8. Applies at most one GitHub mutation per run.
9. Writes workflow metrics as an artifact.

For `route_existing_issue`, the selected issue must already be an eligible
`Todo` Project item. The Action adds the `needs-human-review` label before
moving the Project status to `Needs Human Acceptance`.

For `create_issue`, the Action creates one issue, adds it to the Project, moves
it to `Needs Human Acceptance`, and adds `needs-human-review`. If the queue
mutation fails before the issue reaches the final state, the Action closes the
created issue with an explanatory comment.

## Slack Behavior

`Triage Queue Refill` does not send Slack messages directly.

Slack notification is delegated to the separate Human Acceptance gate workflow.
When this Action successfully adds the `needs-human-review` label, the existing
human acceptance notification flow is responsible for notifying the user in
Slack.

This avoids duplicate Slack messages:

- queue already has at least three `Needs Human Acceptance` issues: no Slack;
- model returns `no_action` or `needs_clarification`: no Slack;
- GitHub mutation fails: no Slack;
- one issue is successfully routed or created and labeled
  `needs-human-review`: Slack is sent by the Human Acceptance workflow, not by
  this Action.

## Required Configuration

- `GH_PROJECT_PAT`: GitHub token with Project V2 read/write and issue write
  access. It is used instead of `GITHUB_TOKEN` so downstream label-triggered
  workflows can run.
- `DEEPSEEK_API_KEY`: required only when refill is needed.
- `DUUMBI_PROJECT_NUMBER`: the GitHub Project V2 number.
- `DUUMBI_PROJECT_OWNER`: optional; defaults to the repository owner.
- `DUUMBI_PROJECT_OWNER_TYPE`: optional; use `user` or `organization` only when
  inference from the GitHub event is not enough.
- `DEEPSEEK_MODEL`: optional; defaults to `deepseek-v4-pro`.
- Existing GitHub label: `needs-human-review`.

## Failure Policy

The Action fails closed. If Project V2 cannot be read, required status fields
are unavailable, configuration is invalid, the model response is malformed, or a
GitHub mutation cannot be safely completed, it stops without silently routing
work.
