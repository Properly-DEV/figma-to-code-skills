# Update mode — existing project

Use this mode when the project already exists and Figma has changed.

---

## Step 1 — Establish what changed

Ask the user:

> "What changed in Figma? (e.g. new colours, updated spacing, new text styles, everything)"

If they don't know — fetch the new `variables.json` and compare with the current `tokens.css`.

## Step 2 — Token diff

Read the existing `tokens/tokens.css` and the new `variables.json`. Compare:

```bash
cat tokens/tokens.css
cat variables.json
```

Find three categories of changes:

**New tokens** — in variables.json, missing from tokens.css
```
+ --colors-brand-950: #00291b   ← NEW
```

**Changed values** — token exists in both but value differs
```
~ --colors-brand-500: #00ec88 → #00f090   ← CHANGED
```

**Removed tokens** — in tokens.css, missing from variables.json
```
- --colors-neutral-150: #d0d0d0   ← REMOVED
```

## Step 3 — Report before making changes

Before changing anything, show the user the diff:

```
Changes found in tokens:

New (3):
  --colors-brand-950: #00291b
  --spacing-10: 40px
  --radius-2xl: 32px

Changed (1):
  --colors-brand-500: #00ec88 → #00f090

Removed (2):
  --colors-neutral-150  ⚠️ may be used in components
  --shadow-xs           ⚠️ may be used in components

Continue?
```

**Warning for removed tokens:** if a token was used in components, removing it will cause visual breakage. Always warn the user — let them decide whether to remove or keep as deprecated.

## Step 4 — Update tokens.css

After user confirmation:

1. Add new tokens to the appropriate sections
2. Update changed values
3. Do **not** automatically remove deprecated tokens — comment them out with a note:

```css
/* DEPRECATED — removed from Figma [date]. Check usage before deleting. */
/* --colors-neutral-150: #d0d0d0; */
```

## Step 5 — Update tailwind.config.ts

This is critical — **tokens.css and tailwind.config.ts must stay in sync.**

For each change in tokens.css:

**New tokens:**
1. Determine the mapping target (is it semantic `fg`/`bg`/`border` or a primitive scale?)
2. Add the corresponding entry to the correct section of tailwind.config.ts

```ts
// New token: --colors-brand-950: #00291b
// → Add to colors.brand object:
brand: {
  // ...existing entries
  950: "var(--colors-brand-950)",  // ← NEW
}
```

**Changed values:** No change needed in tailwind.config.ts — it references `var(--token-name)`, not the hex value. The CSS variable update in tokens.css is enough.

**Removed tokens:**
1. Comment out the corresponding entry in tailwind.config.ts (matching the deprecation in tokens.css)
2. Add a note:

```ts
// DEPRECATED — removed from Figma [date]. Check usage before deleting.
// 150: "var(--colors-neutral-150)",
```

## Step 6 — MCP styles (if available)

If Figma MCP is available — check whether text styles or shadows also changed. Apply the same diff process for the Typography and Shadows sections of tokens.css, and update the corresponding `fontSize` or `boxShadow` entries in tailwind.config.ts.

## Step 7 — Update CLAUDE.md

In the "Figma → Last export" section, enter today's date.

If there were deprecated tokens — add them to TODO.md (create if it doesn't exist):
```
- [ ] Check usage of --colors-neutral-150 in components (deprecated in tokens.css and tailwind.config.ts)
```
