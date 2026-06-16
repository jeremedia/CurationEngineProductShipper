# AGENTS.md - CE Product Shipper

> **Status audit (2026-06-12):** Active Codex operating instructions for the
> CE ship-tracker repo. Refreshed with live `gh issue list` evidence; current
> open ship-blocker examples include `#54`, `#50`, `#18`, and `#6`. GitHub
> issue state is volatile, so re-run the commands below before stating current
> priority.

## Current Status

This repository is the GitHub issue tracker and PM orientation space for CE
v1.0 shipping work. It contains no application implementation code;
implementation lives in the owning CE subprojects. Non-code artifacts such as
local semantic-search data may still exist in the checkout.

Verified on 2026-06-12 with:

```bash
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🔴 ship-blocker"
gh issue list --repo jeremedia/CurationEngineProductShipper --state open --label "🟢 ready-for-work"
```

The active tracker focus includes Web Native v1 work and release command
surface work. Do not rely on the old "90 days from today" text as the current
schedule.

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
