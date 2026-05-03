# AGENTS.md - CE Product Shipper

## Current Status

This repository is the GitHub issue tracker and PM orientation space for CE
v1.0 shipping work. It contains documentation only; implementation lives in the
owning CE subprojects.

Verified on 2026-05-03 with:

```bash
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🔴 ship-blocker"
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🟢 ready-for-work"
```

The active tracker focus now includes Web Native v1 work. Do not rely on the
old "90 days from today" text as the current schedule.

## Operating Rule

Use GitHub issues as source of truth. Before claiming current priority, run
`gh issue list` or inspect the relevant issue directly.

## Priority Query

```bash
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🔴 ship-blocker"
```

Ready work:

```bash
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🟢 ready-for-work"
```

## Labels

| Label | Meaning |
|-------|---------|
| `🔴 ship-blocker` | Must resolve for v1.0 |
| `🟢 ready-for-work` | Unblocked and available |
| `🔵 in-progress` | Already claimed |
| `🟡 nice-to-have` | Defer unless it directly unblocks v1.0 |
| `⚫ wontfix` | Not part of v1.0 |

## Boundaries

- Do not add technical implementation details here unless they point to the
  owning project and issue.
- Do not create local-only plans that are disconnected from GitHub issues.
- For active CE architecture, prefer the root CE docs and `ce-core` source over
  the historical architecture notes in this repo.

