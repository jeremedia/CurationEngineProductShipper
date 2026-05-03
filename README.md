# Curation Engine Product Shipper

> **Status audit (2026-05-03):** Product ship-tracker documentation. Verify current priorities with GitHub issues before planning work.

## Status

GitHub issue tracker for CE v1.0 shipping work.

Verified on 2026-05-03 with `gh issue list` against
`jeremedia/CurationEngineProductShipper`.

Current visible tracker focus has shifted from the original generic 90-day plan
to Web Native v1 work. Open ship-blocker examples include:

- `#54` - Web Native V1 Critical Path: Minimum CE Browser Slice
- `#50` - Web Native 9.1: Create golden fixtures for one supported end-to-end exhibit
- `#18` - Web Native 1.1: Define Web Builder lifecycle parity with Unity Builder semantics
- `#7` - CE Unity MCP Integration: Enable Curator-Driven Exhibition Creation

Use GitHub issues as the source of truth. This repository has no app code and
no `docs/` directory in the current checkout.

## Purpose

This repo is mission-control documentation and issue triage for shipping CE. It
does not own implementation. Code and runtime docs live in the owning projects:

- `ce-core` - shared Unity package and active MCP code
- `CE_Core_HDRP_Unity6` - HDRP desktop authoring project
- `CE_WebGL_URP_Unity6` - URP/WebGL consumer project
- `ZICE_RAILS_APP` - Rails backend
- `ce-collections` - collections/IIIF service

## Daily Commands

```bash
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🔴 ship-blocker"
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🟢 ready-for-work"
gh issue view ISSUE --repo jeremedia/CurationEngineProductShipper
gh issue edit ISSUE --repo jeremedia/CurationEngineProductShipper --add-label "🔵 in-progress" --remove-label "🟢 ready-for-work"
gh issue comment ISSUE --repo jeremedia/CurationEngineProductShipper --body "Progress: ..."
```

## Labels

| Label | Meaning |
|-------|---------|
| `🔴 ship-blocker` | Must resolve for v1.0 |
| `🟢 ready-for-work` | Unblocked and available |
| `🔵 in-progress` | Someone is already working |
| `🟡 nice-to-have` | Defer unless it directly unblocks v1.0 |
| `⚫ wontfix` | Not part of v1.0 |

## Local Documents

| File | Current use |
|------|-------------|
| `README.md` | Current tracker orientation |
| `AGENTS.md` | Codex operating instructions for this repo |
| `CLAUDE.md` | Legacy Claude Code mirror |
| `SHIP_MANIFESTO.md` | Historical 90-day rally document |
| `CE_ARCHITECTURE_DOCUMENTATION.md` | Historical assistant-oriented CE architecture notes |
| `CE_PHILOSOPHY_FOR_CEA.md` | Conceptual CEA philosophy notes |

## Rule

Before using or editing a local plan in this repo, verify the current issue
state with `gh`. Do not treat old week-by-week text as current schedule without
issue evidence.
