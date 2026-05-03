# CLAUDE.md - CE Product Shipper

> **Status audit (2026-05-03):** Product ship-tracker documentation. Verify current priorities with GitHub issues before planning work.

## Status

Legacy Claude Code orientation for the CE shipping tracker. For Codex, keep
`AGENTS.md` and this file aligned.

Verified on 2026-05-03 with `gh issue list` against
`jeremedia/CurationEngineProductShipper`.

## What This Is

This repository is mission control for GitHub issues. It has no implementation
code and no local `docs/` directory in the current checkout.

## Current Tracker Truth

Use GitHub issues as source of truth. The visible active focus includes Web
Native v1 issues, not only the original generic 90-day milestone plan.

Useful commands:

```bash
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🔴 ship-blocker"
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🟢 ready-for-work"
gh issue view ISSUE --repo jeremedia/CurationEngineProductShipper
```

## Role

When working here:

1. Inspect current issues before stating priorities.
2. Tie local documentation or planning changes back to an issue when possible.
3. Keep implementation details in the owning repo docs.
4. Treat `SHIP_MANIFESTO.md` and old week-by-week plans as historical unless
   current issue labels confirm them.

## Owning Projects

- `ce-core` - shared Unity package and active MCP code
- `CE_Core_HDRP_Unity6` - HDRP desktop authoring project
- `CE_WebGL_URP_Unity6` - URP/WebGL consumer project
- `ZICE_RAILS_APP` - Rails backend
- `ce-collections` - collections/IIIF service
