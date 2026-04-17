# Stage 1 — Tokens (two phases: local files first, then Figma MCP)

Build `tokens/tokens.css` in two phases. **Phase 1:** process all local files (variable JSONs, fonts, icons). **Phase 2:** fetch remaining data from Figma MCP (typography details, shadows) — ask user for Figma link only once, at the end.

This file is the **single source of truth** for all visual values. Tailwind references these CSS custom properties in `tailwind.config.ts` (generated in Stage 2). No hex values or pixel numbers should ever appear in components — only Tailwind utility classes that resolve to these variables.

---

## Source A — Figma variable export

Native Figma variable export (Figma → Resources → Variables → Export). Supports two layouts:

**Multiple files** — `variables/` folder with one `.json` per collection:
```
variables/
├── primitives.json    (colors, spacing, radius)
├── semantic.json      (fg, bg, border tokens)
└── components.json    (component-specific tokens)
```
Read all `.json` files in the folder. Each file has the same structure — merge all `collections` arrays before processing.

**Single file** — `variables.json` at project root with all collections in one file.

The format of each file is always:

```json
{
  "collections": [
    {
      "name": "Colors",
      "modes": [
        { "name": "Dark", "variables": [
          { "name": "Brand/500", "value": "#00c070", "type": "COLOR" }
        ]}
      ]
    }
  ]
}
```

**How to merge multiple files:** concatenate all `collections` arrays into one flat list. If two files define the same collection name — merge their variables (last file wins on conflicts).

### Theme mode (required step — ask before generating)

Ask the user which theme setup they need:

> "Which theme mode does this project use?"
> 1. **Dark only** — single dark theme, no switching
> 2. **Light only** — single light theme, no switching
> 3. **Both (light + dark)** — two themes with switching

**Dark only or Light only** → generate a single `:root {}` block. No `darkMode` in Tailwind config.

**Both** → generate `:root {}` for light + a second block for dark. Ask which selector:
> "How should dark mode be triggered — `.dark` class (Tailwind default) or `[data-theme='dark']` attribute?"

Use their answer for the dark block and pass it to Stage 2 (Tailwind config `darkMode` field).

**How to process:** iterate over `collections` → `modes[].variables`. Each variable becomes a CSS custom property:

```
collection.name / variable.name  →  --token-name
Colors / Brand/500               →  --colors-brand-500
Colors / FG/Primary              →  --colors-fg-primary
Spacing / 4                      →  --spacing-4
Radius / MD                      →  --radius-md
```

Naming rules:
- Slash `/` in name → hyphen `-`
- Uppercase → lowercase
- Keep exact names from Figma — do not translate, do not abbreviate
- Type `FLOAT` → add `px` for spacing/radius/size; leave as a number for font-weight and similar

---

## Source B — Figma MCP (deferred to end of Stage 1)

Figma variable exports give **incomplete typography** — typically only `fontSize` and `lineHeight`. Properties like `fontWeight`, `letterSpacing`, and `fontFamily` live in Figma **text styles**, not variables. Shadows are also not exportable as variables.

**Do not ask for Figma links during Source A processing.** First complete everything that can be done from local files (variables, fonts, icons). Then at the end of Stage 1, present the user with a single request for all MCP-dependent data:

> "I've processed all the local files. To complete the tokens, I need data from Figma that can't be exported as variables:
>
> 1. **Typography details** — font-weight, letter-spacing, and font-family for each text style
> 2. **Shadows** — all shadow/elevation styles
>
> Can you share a Figma link to the section with typography and shadow styles? I'll fetch everything in one go via MCP."

Use `get_design_context` with the provided node to fetch:

**Text styles** — each style is a set of:
- `fontSize`, `lineHeight`, `fontWeight`, `fontFamily`, `letterSpacing`
- The Figma style name becomes the token prefix: `Heading/1` → `--heading-1-size`, `--heading-1-line-height`, `--heading-1-weight`

**Effects (shadows)** — each Drop Shadow effect:
- `x`, `y`, `blur`, `spread`, `color` (with opacity)
- Name: `Shadow/SM` → `--shadow-sm`

After fetching, update `tokens/tokens.css` with the missing typography properties and shadow tokens. Also update `@font-face` if MCP revealed a font name that wasn't known from local files.

**If MCP is unavailable:** do not leave empty sections. Ask the user to describe the values manually (e.g. 'Heading 1 is 56px, line-height 64px, weight 500') or paste a Figma screenshot.

If the user cannot provide data — keep tokens.css with the available values and add `/* INCOMPLETE: missing font-weight */` or `/* MISSING: shadows */` comments. Do not continue to Stage 2 without informing the user about gaps.

---

## Output format — tokens.css

**Single mode (light only):**

```css
/* ============================================================
   [Project name] — Design Tokens
   Source: Figma Variables + MCP Styles
   Generated: [date]
   ============================================================ */

:root {

  /* ── Colors: Base ───────────────────────────────────────── */
  --colors-base-white: #ffffff;
  --colors-base-black: #000000;

  /* ── Colors: Neutral ────────────────────────────────────── */
  --colors-neutral-25:  #f8f8f8;
  --colors-neutral-500: #595959;

  /* ── Colors: Brand ──────────────────────────────────────── */
  --colors-brand-500: #00ec88;

  /* ── Colors: System ─────────────────────────────────────── */
  --colors-red-500: #fd4710;

  /* ── Colors: Foreground (semantic) ──────────────────────── */
  --colors-fg-primary: #080808;

  /* ── Colors: Background (semantic) ─────────────────────── */
  --colors-bg-primary: #ffffff;

  /* ── Colors: Border (semantic) ──────────────────────────── */
  --colors-border-primary: #a7a7a7;
  --colors-border-focus:   #6388f7;

  /* ── Spacing ────────────────────────────────────────────── */
  --spacing-4: 16px;

  /* ── Radius ─────────────────────────────────────────────── */
  --radius-md: 12px;

  /* ── Typography ─────────────────────────────────────────── */
  --font-family-headings: "Inter", sans-serif;
  --font-family-body:     "Inter", sans-serif;
  --heading-1-size:        56px;
  --heading-1-line-height: 64px;

  /* ── Shadows ────────────────────────────────────────────── */
  --shadow-sm: 0 1px 2px rgba(0,0,0,0.06), 0 1px 10px rgba(0,0,0,0.1);

}
```

**Two modes (light + dark) — example with `.dark` class (Tailwind default):**

```css
:root {
  --colors-brand-500: #00ec88;
  --colors-fg-primary: #080808;
}

.dark {
  --colors-brand-500: #00c070;
  --colors-fg-primary: #ffffff;
}
```

**Two modes with `data-theme` attribute (alternative):**

```css
:root {
  --colors-brand-500: #00ec88;
  --colors-fg-primary: #080808;
}

[data-theme="dark"] {
  --colors-brand-500: #00c070;
  --colors-fg-primary: #ffffff;
}
```

Non-colour collections (Spacing, Radius, Typography, Shadows) that have only one mode are always in `:root` only.

**Rules:**
- One file, each mode in its own block
- Section comments for each category
- Order: Colors (Base → Neutral → Brand → System → FG → BG → Border → Component) → Spacing → Radius → Typography → Shadows → rest
- Zero values not defined in Figma — do not add your own

---

## Verification after generation

Before moving to Stage 2, check:

- [ ] Every colour from variables.json has its token
- [ ] Text styles are complete (size + line-height for each)
- [ ] Fonts are handled (see below)
- [ ] Dark mode block generated if 2 modes found
- [ ] File loads without errors (no typos in names)

### Font handling

Check `assets/fonts/` for font files in this priority order:

**Have `.woff2` files** → use directly. Add `@font-face` before `:root`:
```css
@font-face {
  font-family: "FontName";
  src: url("../assets/fonts/name.woff2") format("woff2");
  font-weight: 400;
  font-display: swap;
}
```

**Have `.ttf` or `.otf` but no `.woff2`** → convert first, then use. Run:
```bash
# Install woff2 tools if not available
which woff2_compress || brew install woff2

# Convert all TTF/OTF files to woff2
for f in assets/fonts/*.ttf assets/fonts/*.otf; do
  [ -f "$f" ] && woff2_compress "$f"
done
```
After conversion, use the generated `.woff2` files for `@font-face`. Keep the original TTF/OTF files in place.

**No font files at all** → font name will come from Figma MCP in Source B. Defer `@font-face` generation until after MCP fetch. If MCP provides a `fontFamily` name, check if it's available on Google Fonts and use as fallback:

```css
/* Fonts — Google Fonts fallback (no local font files) */
@import url("https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600&display=swap");
```

> "No local font files found. Using Google Fonts for [font name]. If you have woff2/ttf/otf files, add them to `assets/fonts/` and let me know — I'll update tokens.css."

Show the user a summary: how many tokens were generated per category.
