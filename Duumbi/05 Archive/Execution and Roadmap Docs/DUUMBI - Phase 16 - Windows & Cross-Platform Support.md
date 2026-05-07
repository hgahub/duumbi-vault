---
tags:
  - project/duumbi
  - spec/phase
status: planned
created: 2026-03-24
updated: 2026-03-24
phase: 16
---
# DUUMBI — Phase 16: Windows & Cross-Platform Support

> Native Windows support: CI, path handling validation, terminal color fallback, and documentation.

---

## Origin

Issues #420–#423 were originally part of **Phase 11 (CLI UX & Developer Experience), Track F**.
They were deferred because Phase 11's primary focus was macOS/Linux UX, and native Windows
testing requires dedicated infrastructure (MSVC toolchain, Windows Terminal verification).

Phase 11 validated cross-platform compatibility at the code level (all paths use `Path::join()`,
`anstream` handles `NO_COLOR`/`CLICOLOR` automatically). Phase 16 adds **CI enforcement** and
**user-facing documentation** for Windows users.

---

## Kill Criterion

> `cargo test --all` passes on `windows-latest` GitHub Actions runner with MSVC toolchain,
> and a new user on Windows 10 (1903+) can `cargo install duumbi` and run `duumbi init`
> successfully without WSL2.

---

## Scope

### Track A: CI Infrastructure

| # | Title | Description |
|---|-------|-------------|
| #420 | Add `windows-latest` to CI matrix | `.github/workflows/ci.yml` — add Windows job alongside existing `ubuntu-latest` |
| #422 | Windows terminal color test | CI step to verify `anstream` auto-detects Windows Terminal capabilities |

### Track B: Path & Compatibility Audit

| # | Title | Description |
|---|-------|-------------|
| #421 | Path separator audit | Validate all module name / path handling uses forward slashes consistently. **Note:** Phase 11 audit found no issues — this issue confirms the audit and closes. |

### Track C: Documentation

| # | Title | Description |
|---|-------|-------------|
| #423 | Windows requirements in README | Document: Win10 1903+ for ANSI colors, MSVC toolchain for linking, `$CC` setup |

### Track D: Platform-Specific Fixes (if needed)

Discovered during CI bring-up. Potential areas:
- File watcher (`notify` crate) behavior on Windows
- `credentials.toml` permissions (Unix `0600` → Windows ACL or no-op)
- Temp directory cleanup (`\\?\` prefix paths)
- C runtime compilation (`cc` crate MSVC detection)

---

## Dependencies

- **Phase 11** (complete) — color, completion, progress infrastructure
- **GitHub Actions** — `windows-latest` runner availability

## Non-Goals

- WSL2-specific testing (already works, tested in Phase 11)
- ARM64 Windows support
- MinGW/Cygwin toolchain support (MSVC only)

---

## Estimated Effort

2–3 days for Tracks A–C. Track D depends on what CI discovers.

---

## Related

- [[DUUMBI - Phase 11 - CLI UX & Developer Experience]] — parent phase
- [[DUUMBI Roadmap Map]] — master roadmap
- GitHub Issues: #420, #421, #422, #423
