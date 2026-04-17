---
name: ds-status-changelog-tw
description: 'Creates and updates STATUS.md and CHANGELOG.md for a design system. Use whenever the user wants to track what''s done, what''s in progress, or log changes - phrases like update the changelog, what''s the status of components, mark this as done, log this change, what''s ready for handoff, update status, or when closing a sprint/milestone on the design system.'
---

# ds-status-changelog

> **Language rule:** All generated files must be written in English.

Manages two living documents that keep designers and developers in sync
about the state of the design system.

## Two documents, two audiences

**STATUS.md** → snapshot of right now. Designers and devs check this
to know what they can use, what's coming, what's blocked.

**CHANGELOG.md** → history of what changed and when. Devs use this
when integrating updates. Designers use it to know when to re-check a component.

## When to read reference files

- To create or fully rewrite STATUS.md → read `references/status-md.md`
- To create or fully rewrite CHANGELOG.md → read `references/changelog-md.md`
- To add a single entry → follow the quick format below, no need to read references

## Quick entry format (no reference file needed)

### Adding a STATUS update for a component
Find the component row and change its status emoji:
- `🔲 Planned` — exists in Figma, not started in code
- `🔧 In Progress` — being built
- `✅ Done` — tokens ✓, React ✓, showcase ✓, Figma sync ✓
- `⚠️ Needs Review` — built but has known issues
- `🚫 Deprecated` — removed or replaced

### Adding a STATUS update for a pattern
Find or add a row in the `## Patterns` table. Use the same emoji set.
`✅ Done` for a pattern means: spec.md ✓, HTML preview ✓, all components exist.

### Adding a CHANGELOG entry
Prepend to the `## Unreleased` section:
```
- [Component] Description of change (Figma node if relevant)
```

When cutting a release, convert `## Unreleased` to `## YYYY-MM-DD` and add new empty `## Unreleased` above.

## Context to gather

Before creating from scratch, check:
- List of all components and their current state
- List of patterns in `/patterns/` if it exists
- Any components with known issues or Figma drift
- Whether there's already a STATUS.md or CHANGELOG.md to update vs. create
- Whether TODO.md exists — if yes, link to it at the bottom of STATUS.md

---

## Finish — TL;DR for user

After completing all steps, print a short summary:

✅ **Done:** [what was created or changed — file names, counts]
⚠️ **Gaps:** [what's missing, blocked, or needs user decision — or "none"]
➡️ **Next:** [suggested next action or skill to run]

Keep it to 3–5 lines. No technical jargon. The user is a designer, not a developer.

Also update `_designer/session-state.md` with what was done and what to do next.
