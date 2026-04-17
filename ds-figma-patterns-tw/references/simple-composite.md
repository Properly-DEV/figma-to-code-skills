# Simple Composite — Path A

**When to read:** The pattern consists of 2–3 known components in a straightforward layout. No new elements. No complex state management. A screenshot is sufficient.

---

## What to extract from the screenshot

Focus on layout precision:

- Spacing between components (gap, padding)
- Alignment (flex direction, justify, align)
- Any visible size constraints (min/max width, fixed heights)
- Which Tailwind class maps to each spacing value (check `tailwind.config.ts`)

If you cannot read exact spacing from the screenshot, note as `[check Figma]`. Do not guess.

---

## What NOT to include in Path A output

- No full React/TSX implementation — spec.md gives structure, AI Agent Instructions gives build directives
- No props API tables — that belongs in ds-figma-to-react-tw
- No Do/Don't tables or usage examples
- Keep it lean: Anatomy + Layout + AI Agent Instructions

---

## Simplified spec.md template (Path A)

```markdown
# {PatternName} — Composite Spec

## Anatomy

{PatternName}
├── {ComponentA}        [existing: /components/{ComponentA}.tsx]
├── {ComponentB}        [existing: /components/{ComponentB}.tsx]
└── {ComponentC}        [existing: /components/{ComponentC}.tsx]

## Layout

- Container: `flex flex-col gap-4 p-6`
- Alignment: `items-start` | `items-center` | `justify-between`
- Width: `w-full` | `max-w-md` | as designed
- {Any other notable layout rule}

## Notes

{Any edge case or clarification.}

## AI Agent Instructions

Implement **{PatternName}** with React + TypeScript + Tailwind.

### Components
- `{ComponentA}` — [existing: /components/{ComponentA}.tsx]
- `{ComponentB}` — [existing: /components/{ComponentB}.tsx]

### Layout
Container: `flex flex-col gap-4 p-6`.
Use only Tailwind classes from tailwind.config.ts — no arbitrary values, no style={{}}.
No application logic needed for this composite.
```

---

## HTML prototype for Path A

Minimal — single state, no tab switcher.

The prototype uses CSS variables from `tokens.css` for browser compatibility (Tailwind CDN can't resolve custom config classes). The spec.md above contains the Tailwind class equivalents.

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{PatternName}</title>
  <link rel="stylesheet" href="../tokens/tokens.css">
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: var(--font-family-body);
      background: var(--colors-bg-primary);
      color: var(--colors-fg-primary);
    }
    /* Pattern layout — CSS variable equivalents of Tailwind classes in spec */
    .pattern {
      display: flex;
      flex-direction: column;
      gap: var(--spacing-4);
      padding: var(--spacing-6);
    }
  </style>
</head>
<body>
  <div class="pattern">
    <!-- Render components here using HTML equivalents with sample data -->
  </div>
</body>
</html>
```

Keep CSS minimal. Every value must be a `var(--token)`.
