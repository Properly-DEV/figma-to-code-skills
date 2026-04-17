# Stage 5 — Final Verification

Check that the project is ready for building the first component.

---

## Checklist

### Tokens
- [ ] `tokens/tokens.css` exists
- [ ] File has a `:root` block with at least: colours, spacing, radius, typography
- [ ] Zero hardcoded values outside `:root` (no `#hex` or `16px` outside tokens)
- [ ] If woff2 fonts existed — `@font-face` is defined
- [ ] If 2 modes found — dark mode block exists (`.dark` class or `data-theme`)

### Tailwind Config
- [ ] `tailwind.config.ts` exists in root
- [ ] Every token from tokens.css has a corresponding Tailwind mapping
- [ ] Semantic tokens mapped per-utility (textColor, backgroundColor, borderColor) — no `bg-bg-*` double prefixes
- [ ] Primitive scales (neutral, brand) mapped in shared `colors` object
- [ ] `content` paths cover `components/`, `patterns/`, `lib/`
- [ ] `darkMode` matches theme mode: omitted for single-theme, `"class"` or `["selector", "..."]` for dual-theme
- [ ] `postcss.config.js` exists in root
- [ ] Typography tokens use `[size, { lineHeight }]` tuple syntax

### Utilities
- [ ] `lib/utils.ts` exists and exports `cn()`
- [ ] Dependencies listed: `tailwindcss`, `postcss`, `autoprefixer`, `class-variance-authority`, `clsx`, `tailwind-merge`

### Icons
_Only if Stage 3 ran (custom SVGs). Skip if using an icon library (Lucide, Heroicons, etc.)._

- [ ] Folder `assets/icons/` exists and is not empty
- [ ] All SVGs use `currentColor`
- [ ] No SVG has a hardcoded colour (`fill="#..."`, `stroke="#..."`)
- [ ] If React components — `index.ts` barrel file exists

### CLAUDE.md
- [ ] File exists in project root
- [ ] Stack section lists React + TypeScript + Tailwind
- [ ] CSS Strategy section describes Tailwind approach (cva, cn, utility classes)
- [ ] Token paths are correct (`tokens/tokens.css` and `tailwind.config.ts`)
- [ ] "Component list is in STATUS.md" note is present

### Folder structure
- [ ] `tokens/` exists
- [ ] `lib/` exists with `utils.ts`
- [ ] `assets/icons/` exists
- [ ] `components/` exists (even if empty)
- [ ] `tailwind.config.ts` in root
- [ ] `postcss.config.js` in root

---

## TODO.md — create if gaps found

If any checklist item failed, or if any values in tokens.css are `[to be filled]`, or if Figma MCP was unavailable during token generation — create `TODO.md` in the project root:

```markdown
# TODO

Items to resolve before the first component can be built.

## Missing token data
- [ ] [Description of missing value, e.g. "Shadow/MD value — Figma MCP was unavailable"]

## Missing assets
- [ ] [Description, e.g. "woff2 font files — currently using Google Fonts fallback"]

## Dependencies
- [ ] [Description, e.g. "Run: npm install tailwindcss postcss autoprefixer class-variance-authority clsx tailwind-merge"]

## To confirm
- [ ] [Description, e.g. "Dark mode approach: .dark class or data-theme attribute?"]
```

Only create TODO.md if there are actual items to record. Do not create an empty file.

---

## Final report

After verification, show the user:

```
✅ Setup complete — [Project name]

Generated:
  tokens/tokens.css      — [N] tokens (colours: X, spacing: X, radius: X, typography: X, shadows: X)
  tailwind.config.ts     — Tailwind mapping (textColor: X, backgroundColor: X, borderColor: X, spacing: X, radius: X, shadows: X, fontSize: X)
  postcss.config.js      — PostCSS config
  lib/utils.ts           — cn() utility
  icons                  — [N] icons ([format])
  CLAUDE.md              — ready

Dependencies required:
  tailwindcss, postcss, autoprefixer, class-variance-authority, clsx, tailwind-merge

Ready to build components.

⚠️ Open items (see TODO.md):
  [list if any]

Next step: build the first component using the ds-figma-to-react-tw skill.
```

If any checklist item failed — list what needs attention before proceeding.
