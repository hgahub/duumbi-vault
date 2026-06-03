---
tags:
  - project/duumbi
  - concept/agent-review
status: active
created: 2026-06-03
updated: 2026-06-03
---

# AI Code Review Service Policy

## Summary

DUUMBI uses layered AI review. Codex self-review is the mandatory contextual
review layer, Copilot is the default automated PR reviewer, CodeRabbit is
advisory when present, and Greptile is a scarce manual deep-review escalation.

## Why it matters

Running every connected AI reviewer on every PR creates noise, burns quota, and
can trap agents in repeated review/fix cycles. Review tools should match the
stage and risk of the change.

## DUUMBI usage

- Use Codex self-review before marking specs or implementation PRs ready.
- Use Copilot review as the default automated PR evidence for file-based Stage
  7 and Stage 9 gates and as baseline implementation PR review evidence.
- Treat CodeRabbit as advisory unless GitHub branch protection requires it.
- Use Greptile only when a developer explicitly requests it for a stable
  high-risk implementation PR, such as complex Rust, compiler/runtime behavior,
  graph invariants, provider/auth, async/concurrency, security, or broad
  cross-module refactors.
- Do not include Greptile in `DUUMBI_REQUIRED_SPEC_REVIEWERS`.
- Do not re-run Greptile after every push. At most one follow-up run should be
  used after blocking feedback, and only with explicit developer authorization.
- For docs-only, spec-only, and routine config-only PRs, do not use Greptile by
  default.

## Sources

- [[DUUMBI - Agentic Development Runbook]]
- [[Structured Agent Review Artifacts]]
- [DUUMBI repo AI code review policy](https://github.com/hgahub/duumbi/blob/main/docs/automation/code-review-policy.md)
- [Greptile .greptile configuration](https://www.greptile.com/docs/code-review/greptile-config)
- [Greptile controlling nitpickiness](https://www.greptile.com/docs/code-review/controlling-nitpickiness)

## Related

- [[Codex Development Toolchain]]
- [[Agent Skills as Operational Playbooks]]
- [[GitHub Project as Execution Source of Truth]]
