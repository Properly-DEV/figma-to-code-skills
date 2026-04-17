---
name: ds-session-start-tw
description: "Run at the start of every new Claude Code session when working on a design system. Triggers: 'where were we', 'what's the status', 'continue working', 'session start', or any first message in a new session about the design system."
---

# Session Start — Gather Context

Read project state and orient before doing any work.

## Step 1 — Read core files

Read in order (skip any that don't exist):
1. `CLAUDE.md` — project conventions, stack, skills
2. `_designer/session-state.md` — what happened last time, what's next
3. `STATUS.md` — what's done, what's in progress
4. `TODO.md` — open gaps, dependencies
5. `CHANGELOG.md` — full history

## Step 2 — Scan components and patterns

```bash
ls components/*.tsx
ls patterns/*.spec.md 2>/dev/null
```

Count: how many components, how many patterns.

## Step 3 — Check for open audit findings

```bash
grep -l "Open:" _designer/audits/*.audit.md 2>/dev/null
```

If any audit has open findings — note them.

## Step 4 — Report to user

Print a short summary:

**Project:** [name from CLAUDE.md]
**Components:** [count] done, [count] in progress
**Patterns:** [count]
**Open issues:** [from TODO.md and audits]
**Last change:** [from CHANGELOG.md]
**Last session:** [from session-state.md, or "unknown" if file missing]

Then ask:
> "What do you want to work on today?"

If the user doesn't know, suggest based on what's open in STATUS.md or TODO.md.
