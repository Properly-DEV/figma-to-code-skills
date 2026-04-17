---
name: ds-preview-tw
description: "Generates a quick standalone HTML preview for a single component, showing all variants, interaction states, and computed design specs. Use when the user says: preview this component, show me the Button, generate a preview, compare with Figma, check the spacing, show design specs, or wants to verify Figma-to-code accuracy for a specific component."
---

# ds-preview-tw — Component Preview

Generates a quick, disposable HTML preview for a single component.
Output: `_preview/{ComponentName}.html` — opened in a browser, compared with Figma side-by-side.

**Not auto-triggered.** Only runs on explicit request.

Preview files are ephemeral — they live in `_preview/` which should be gitignored.

---

## Step 0: Setup

1. Ensure `_preview/` directory exists. If `.gitignore` does not include `_preview/`, add it.
2. Read `CLAUDE.md` for project context (token paths, config, naming conventions).
3. Read the target component `.tsx` file from `components/`.
4. Read `tokens/tokens.css` to understand available tokens and their values.
5. If the target component imports other components (e.g., Dropdown imports Checkbox), read those files too — replicate their HTML structure in the preview instead of inventing new markup.
6. If `_designer/audits/{ComponentName}.audit.md` exists, read it — note any open findings or token gaps to highlight in the preview.

---

## Step 1: Analyze the Component

From the `.tsx` file, extract:

- **Variant axes** — all `cva` variant keys and their values (e.g., `variant: primary | secondary | tertiary`, `size: sm | md | lg`)
- **Interaction states** — which Tailwind state modifiers are used (`hover:`, `focus-visible:`, `active:`, `disabled:`)
- **Loading state** — does the component accept a `loading` prop?
- **Design values per variant** — for each variant combination, collect the Tailwind classes and resolve them to computed values:
  - Spacing & Dimensions: padding, margin, gap, height, width, min-height (in px)
  - Colors: background, text, border colors (as hex values + token name)
  - Typography: font-size, line-height, font-weight, letter-spacing
  - Border: border-radius, border-width, border-style

Cross-reference Tailwind classes with `tokens/tokens.css` and `tailwind.config.ts` to resolve token names and actual values.

---

## Step 2: Generate the Preview HTML

Read `references/preview-template.md` for the HTML template structure.

The preview page contains:

### 2a. Header
- Component name and source file path
- Date generated
- Audit status (if audit file exists: open findings count)

### 2b. Variant Grid
- Matrix layout: one axis across columns, another down rows
- If only one axis: single row of variants
- Each cell contains:
  - The rendered component (static HTML mirroring React output)
  - A label pill below it (system font, `rgba(128,128,128,0.3)` background)

### 2c. Interaction States (Interactive)
- For each variant, show **working interactive examples** — clicks should toggle/select/check as appropriate.
- Hover states work via CSS `:hover` natively.
- Show disabled state (native `disabled` or `aria-disabled`).
- Show loading state if the component supports it.
- Use label pills to identify each state.

### 2d. Computed Design Specs

One specs table per component (or per variant if significantly different). Three columns — always:

| Tailwind class | CSS variable | Primitive value |

Group rows by category: Typography, Colors, Spacing & Dimensions, Border, Shadow, Icon sizes. For colors, include an inline swatch. List ALL tokens used — resolve every variable to its actual value from `tokens/tokens.css`.

---

## Step 3: Styling Rules

- **Component rendering:** Use design tokens from `tokens/tokens.css` via `<link>` — the component must look identical to the React version.
- **Labels and specs:** Use system fonts (`-apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif`) — never DS fonts for UI chrome.
- **Label pills:** `background: rgba(128,128,128,0.3); color: #fff; padding: 2px 8px; border-radius: 4px; font-size: 11px;`
- **Background:** Render the component on its native background. Read the root background from `tokens.css` (e.g., `--colors-bg-primary`). If the token uses a dark value, the page background is dark. If light, light. The preview adapts — it is theme-agnostic.
- **Specs tables:** Subtle styling, system font, low visual weight — they support the component, not compete with it.
- **Minimal vanilla JavaScript** for interactive states. All interactive elements must be clickable — toggling, selecting, checking must work. Add a `<script>` at end of `<body>` with vanilla event listeners. No frameworks, no external deps.
- **No sidebar, no navigation.** Single component, single page.

---

## Step 4: Write the File

1. Write the generated HTML to `_preview/{ComponentName}.html`.
2. If a previous preview exists for this component, overwrite it.

---

## Quality Checklist

Before delivering, verify:

- [ ] Every variant combination is shown (complete matrix, nothing skipped)
- [ ] Interaction states are labeled (default, hover, disabled, loading)
- [ ] Design specs table is present for each variant with actual px/hex values
- [ ] Token names appear next to computed values in specs
- [ ] Component uses DS tokens from `tokens.css` (no hardcoded colors or sizes)
- [ ] Labels use system font, not DS font
- [ ] Label pills use `rgba(128,128,128,0.3)` background
- [ ] Background color matches the component's native context
- [ ] File opens in browser without a server (no fetch, no imports, no build)
- [ ] Audit findings (if any) are noted in the header
- [ ] Interactive elements respond to clicks (toggle, select, check/uncheck)
- [ ] Script is vanilla JS — no frameworks, no external deps
- [ ] Specs table has 3 columns: Tailwind class / CSS variable / primitive value
- [ ] Sub-components match the HTML structure of their source .tsx files
- [ ] SVG stroke-width uses token variables (`var(--icon-stroke-*)`) matching the icon size — never hardcoded "2"

---

## Finish — TL;DR for user

After completing all steps, print a short summary:

Done: [file created, variant count, what's shown]
Gaps: [anything missing or needing user decision — or "none"]
Next: [suggested next action — e.g., "open in browser and compare with Figma"]

Keep it to 3-5 lines. No technical jargon. The user is a designer, not a developer.

Also update `_designer/session-state.md` with what was done and what to do next.
