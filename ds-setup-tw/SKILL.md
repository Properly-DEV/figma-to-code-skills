---
name: ds-setup-tw
description: "Runs the full setup of a new React + Tailwind design system project OR updates tokens in an existing project. Use this skill whenever the user says 'start a new project', 'project setup', 'I have exported files from Figma', 'prepare the design system project', 'update tokens', 'Figma changed', or when the folder contains variables.json and/or SVG assets. The skill handles the entire process from raw Figma exports to a ready foundation for building components with Tailwind CSS."
---

# Project Setup — React + Tailwind Design System

Walk the user through the full project setup. Each stage is separate — complete it, confirm with the user, then move on.

## Mode detection

At the start, determine which mode to use:

- **New project** — no `tokens/tokens.css`, no `CLAUDE.md` → run Stages 1–5
- **Update** — `tokens/tokens.css` or `CLAUDE.md` already exists → read `references/update.md` and apply only what changed

If unsure which mode — ask the user.

## File access

Project files are on the user's computer. Use `bash_tool` to read them:

```bash
# Check the project folder contents
ls -la
# Check for variables — folder or single file
ls variables/ 2>/dev/null || cat variables.json
ls assets/icons/
```

If `bash_tool` is not available — ask the user to paste the contents directly in the chat.

## What should be in the folder

Variables (**required** — one of the two):
- `variables/` folder with multiple `.json` files (e.g. `primitives.json`, `semantic.json`, `components.json`) — one per Figma collection
- `variables.json` — single file with all collections (legacy, still supported)

Assets (optional):
- `assets/fonts/` — woff2 files (if missing, the skill will use Google Fonts)
- `assets/icons/` — custom SVG icons (skip if using Lucide)
- `assets/images/` — illustrations, placeholders, decorative graphics (SVG, PNG @2x, WebP)
- `assets/logos/` — logo variants as SVG (e.g. `logo-full.svg`, `logo-icon.svg`)

If neither `variables/` folder nor `variables.json` exists — stop and tell the user how to export from Figma.

### Asset file naming
Figma exports files with property names like `type=Dark, variant=Full.svg`. Before proceeding, rename all asset files to kebab-case:
- Strip property keys (`type=`, `variant=`, `size=`)
- Keep only values, lowercase, hyphens instead of spaces
- Remove parentheses, commas, special characters
- Example: `type=Dark, variant=Full.svg` → `logo-dark-full.svg`
- Example: `Ethereum (ETH).svg` → `ethereum-eth.svg`

---

## Stage order

### Stage 1 — Tokens (two phases)
Read `references/tokens.md`.

**Phase 1 — local files:** Process variable JSONs, convert fonts (TTF/OTF → woff2 if needed), generate initial `tokens/tokens.css` with everything available from files.
**Phase 2 — Figma MCP:** Ask the user for a single Figma link to fetch typography details (font-weight, letter-spacing, font-family) and shadows. Update tokens.css with the fetched data.

Output: `tokens/tokens.css` (complete)

### Stage 2 — Tailwind Config
Read `references/tailwind-config.md`.

Input: `tokens/tokens.css` (generated in Stage 1)
Output: `tailwind.config.ts`, `postcss.config.js`, `lib/utils.ts`

### Stage 3 — Icons (conditional)
**Skip if using an icon library (Lucide, Heroicons, etc.)** — no files to process. Go straight to Stage 4.

**Run only if the project has custom SVG icons** in `assets/icons/`. Ask the user before starting:
> "Do you have custom icon SVGs exported from Figma, or are we using an icon library like Lucide?"

If custom SVGs → read `references/icons.md`.
Input: `assets/icons/` folder with SVG files
Output: `assets/icons/` with optimised SVGs + React components

### Stage 4 — CLAUDE.md
Read `references/claude-md.md`.

Input: everything generated in Stages 1–3
Output: `CLAUDE.md` in the project root

### Stage 5 — Verification
Read `references/checklist.md`.

Check that the project is ready for building the first component.
This stage also creates `TODO.md` if any gaps were found during setup.

---

## Rules

- Complete one stage at a time. Do not proceed without user confirmation.
- Stack is **React + TypeScript + Tailwind CSS**.
- If Figma MCP is unavailable — ask the user to fill in missing values. Details in `references/tokens.md`.
- Do not create files the user did not ask for. Ask before adding anything outside the list.

## Required dependencies

The project must have these npm packages installed. Remind the user at the end of Stage 2 if they are missing:

```
tailwindcss
postcss
autoprefixer
class-variance-authority
clsx
tailwind-merge
```

---

## Finish — TL;DR for user

After completing all stages, print a short summary:

✅ **Done:** [what was created or changed — file names, counts]
⚠️ **Gaps:** [what's missing, blocked, or needs user decision — or "none"]
➡️ **Next:** [suggested next action or skill to run]

Keep it to 3–5 lines. No technical jargon. The user is a designer, not a developer.

Also update `_designer/session-state.md` with what was done and what to do next.
