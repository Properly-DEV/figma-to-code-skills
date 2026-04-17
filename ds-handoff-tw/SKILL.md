---
name: ds-handoff-tw
description: 'Generates and audits GitHub handoff documentation for a React design system - CLAUDE.md, README.md, CONTRIBUTING.md. Use this skill whenever the user wants to close, hand off, or prepare a design system for delivery to developers or clients. Triggers include: prepare handoff, write README, document the design system, what do we need for GitHub, close the project, prepare for developer, write CLAUDE.md, or any request to document a design system repo for external use.'
---

# ds-handoff-docs

> **Language rule:** All generated files must be written in English.

Produces three handoff documents that make a design system repo self-explanatory
for anyone who opens it on GitHub — a developer, a new Claude session, or a client.

## Context to gather first

Before writing anything, check (or ask) for:
- Project name & client/brand name
- Figma file URL and last export date
- Token file location (`tokens.css` or similar)
- Component list with their status
- Deploy URL if showcase exists
- Who the intended audience is (internal devs, client devs, external contributors)

Read project files if available: `tokens.css`, any `*.tsx` components, existing `CLAUDE.md`, `README.md`.

## Files to produce

### 1. CLAUDE.md — UPDATE (do not rewrite from scratch)
→ Read `references/claude-md.md` for what to update and what to leave alone.
CLAUDE.md already exists — it was created by `ds-setup-tw` at the start of the project.
At handoff, update only what has changed:
- Component status table (mark all completed components as ✅ Done)
- Deploy URL (add showcase URL if it exists)
- Last Figma export date

### 2. README.md — GENERATE (new file for GitHub)
→ Read `references/readme-md.md` for structure and rules.
The "face" of the repo — humans read this first on GitHub.

### 3. CONTRIBUTING.md — GENERATE (new file for GitHub)
→ Read `references/contributing-md.md` for structure and rules.
How to add a component, sync tokens from Figma, run the showcase locally.

## Output rules

- Generate README.md and CONTRIBUTING.md; update (not rewrite) CLAUDE.md
- Use the actual token names from `tokens.css` in examples, not placeholders
- Keep code examples short — one real example beats three generic ones
- No marketing language in README — designers will read it too
- CLAUDE.md is for AI, so be precise and structured, not friendly

---

## Finish — TL;DR for user

After completing all steps, print a short summary:

✅ **Done:** [what was created or changed — file names, counts]
⚠️ **Gaps:** [what's missing, blocked, or needs user decision — or "none"]
➡️ **Next:** [suggested next action or skill to run]

Keep it to 3–5 lines. No technical jargon. The user is a designer, not a developer.

Also update `_designer/session-state.md` with what was done and what to do next.
