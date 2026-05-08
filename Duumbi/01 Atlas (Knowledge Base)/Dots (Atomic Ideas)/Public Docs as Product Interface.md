---
tags:
  - project/duumbi
  - concept/documentation
  - doc/product-spec
status: active
source: repository-inspection
created: 2026-05-07
updated: 2026-05-07
---

# Public Docs as Product Interface

## Summary

DUUMBI public documentation is part of the product interface. It should describe behavior users can depend on, not internal planning or unstable execution state.

## Why it matters

Docs drift is product drift. If docs describe stale commands, schemas, registry behavior, or architecture, agents and users will make wrong changes with confidence.

## DUUMBI usage

- Use public docs for installation, quickstart, CLI reference, config reference, registry usage, and supported workflows.
- Use Obsidian for rationale, architecture trade-offs, source-backed decisions, and curation context.
- Use GitHub PR review and CI evidence to decide whether docs updates are required.
- Do not publish speculative roadmap details as user documentation unless they are explicitly marked as future-facing product communication.

## Sources

- [duumbi-web](https://github.com/hgahub/duumbi-web)
- Local source: `/Users/heizergabor/space/hgahub/duumbi-web/README.md`
- Local source: `/Users/heizergabor/space/hgahub/duumbi-web/docs/src/content/docs/reference/cli.md`
- Local source: `/Users/heizergabor/space/hgahub/duumbi-vault/Duumbi/01 Atlas (Knowledge Base)/Works (Developed Materials)/DUUMBI - PRD.md`

## Related

- [[Static Website and Docs Publishing]]
- [[Obsidian Vault as Agent Knowledge Substrate]]
- [[GitHub Project as Execution Source of Truth]]
- [[Structured Agent Review Artifacts]]
