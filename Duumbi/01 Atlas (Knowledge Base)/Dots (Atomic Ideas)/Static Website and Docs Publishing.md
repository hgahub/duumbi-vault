---
tags:
  - project/duumbi
  - concept/documentation
  - concept/web
status: active
source: repository-inspection
created: 2026-05-07
updated: 2026-05-07
---

# Static Website and Docs Publishing

## Summary

`duumbi-web` owns the public DUUMBI website and documentation surfaces, built with Astro, Tailwind CSS, and Starlight docs.

## Why it matters

The website and docs are product interfaces. They shape what users believe DUUMBI does, how they install it, how they use the registry, and which APIs or concepts are stable enough to learn.

## DUUMBI usage

```mermaid
flowchart LR
    source["duumbi-web source"] --> landing["Astro landing site<br/>duumbi.dev"]
    source --> docs["Starlight docs<br/>docs.duumbi.dev"]
    docs --> gettingStarted["Getting started"]
    docs --> guides["Guides"]
    docs --> reference["Reference"]
    docs --> registry["Registry docs"]
    vault["duumbi-vault"] --> rationale["Architecture and rationale"]
    rationale --> docs
```

- Put user-facing how-to, reference, and onboarding docs in `duumbi-web/docs`.
- Put durable rationale, architecture context, source mapping, and documentation policy in Obsidian.
- Keep marketing and public positioning in `duumbi-web`, not the vault.
- Update Obsidian only when a docs change reflects a durable product or architecture decision.

## Sources

- [duumbi-web](https://github.com/hgahub/duumbi-web)
- Local source: `/Users/heizergabor/space/hgahub/duumbi-web/README.md`
- Local source: `/Users/heizergabor/space/hgahub/duumbi-web/docs/src/content/docs/registry/index.md`
- Local source: `/Users/heizergabor/space/hgahub/duumbi-infra/stack-platform.ts`

## Related

- [[Public Docs as Product Interface]]
- [[DUUMBI Azure Infrastructure Model]]
- [[DUUMBI Repository Responsibility Model]]
- [[DUUMBI Repository Map]]
