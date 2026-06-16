# The Curation Engine Ship Manifesto

> **Status audit (2026-06-12): Historical rally document.**
> Treat the whole body below as motivational provenance, not current release
> doctrine. Live `gh issue list` evidence shows open ship-blockers remain,
> including Web Native v1 and release-command-surface work. Use `COVENANT.md`,
> `RELEASE_CHECKLIST.md`, and GitHub issues in
> `jeremedia/CurationEngineProductShipper` for current release definition,
> technical scope, testing posture, platform sequencing, success metrics,
> schedule, blockers, and active focus. Do not read the old "90 days from
> today", "ON TRACK", "Blockers: NONE", "Complete Testing can wait", or "Mac
> desktop first" text below as live status.

## After 8 Years, We Ship in 90 Days

### The Breakthrough

We spent 8 years building a powerful 3D engine. We almost spent 2 more years building traditional UI. Then we realized: **natural language IS the UI**.

While others click through menus, our users will simply say:
> "Create a minimalist gallery with my photography collection"

And it will exist.

### The Brutal Focus

**v1.0 ships when this works:**
1. User describes what they want
2. MCP translates to engine commands
3. Unity renders the space
4. User exports to share

**Everything else is v2.0.**

### The Anti-Patterns We Reject

❌ **Perfect Code** - We have 7,925 TODOs. They can wait.
❌ **Complete Testing** - We need critical path tests, not 100% coverage.
❌ **Traditional UI** - Natural language is faster to ship and easier to use.
❌ **Feature Completeness** - Ship the core, iterate based on usage.
❌ **Platform Parity** - Mac desktop first. Vision Pro later.

### The Patterns We Embrace

✅ **Ship Beats Perfect** - Users can't use vaporware
✅ **Natural Language First** - Skip 2 years of GUI development
✅ **Dogfood Daily** - We use what we build
✅ **Fix Forward** - Ship v1.0, fix in v1.1
✅ **User Feedback** - Real usage beats imagined requirements

### The 90-Day Sprint

**Days 1-30:** Make it work (tests + MCP)
**Days 31-60:** Make it stable (critical bugs only)
**Days 61-90:** Make it shippable (docs + beta)

### The Daily Question

Before starting any task, ask:
> "Does this help ship v1.0 in 90 days?"

If no, stop immediately.

### The Technical Decisions

1. **MCP as Primary UI** - Natural language interface via Model Context Protocol
2. **Unity as Renderer** - 8 years of proven 3D engine work
3. **Rails as Backend** - Existing API for persistence and services
4. **WebGL as Distribution** - Standalone exhibitions anyone can view

### The Success Metrics

**Week 1 after launch:**
- 100 galleries created
- 10 exhibitions shared publicly
- 1 museum professional says "this changes everything"

### The Team

**Director:** Jeremy Roush - 8 years of vision
**PM:** Claude Code - Shipping machine
**Engine:** Unity 6 + HDRP - Battle-tested renderer
**Backend:** Historical Rails backend snapshot - reliable persistence

### The Promise

To every museum curator, educator, and art lover who's been waiting:

**In 90 days, you'll create exhibitions with words, not clicks.**

### The Commitment

Every line of code asks: "Does this ship v1.0?"
Every bug fix asks: "Is this ship-blocking?"
Every feature asks: "Can this wait for v1.1?"

### The End Game

Curation Engine has been in development longer than Starcraft 2, Duke Nukem Forever, or Team Fortress 2.

**It's time to ship.**

---

*"The perfect museum platform doesn't exist. But a good-enough one that ships does."*

**Ship Date: 90 days from today**
**Status: ON TRACK**
**Blockers: NONE**

> Historical footer preserved from the rally document. It contradicts current
> tracker state as of the 2026-06-12 audit; re-check GitHub issues before
> making any ship-readiness claim.

Let's fucking ship. 🚀
