# Stage 3 — Icons

Prepare SVG assets for use in React components.

---

## What the user has done (already complete)

SVG export from Figma — files are in `assets/icons/`.

## What Claude does

### Step 1 — Check what's there

```bash
ls assets/icons/
```

Note:
- Number of files and naming convention (kebab-case? with `icon-` prefix?)
- Whether SVGs have hardcoded colours (`fill="#000"`) or already use `currentColor`
- Whether all have a `viewBox`

Report to the user what you found before changing anything.

### Step 2 — Fix SVGs

For each SVG file, check and fix:

**Colour — replace hardcoded with currentColor:**
```xml
<!-- ❌ -->
<path fill="#000000" .../>
<path stroke="#1A1A1A" .../>

<!-- ✅ -->
<path fill="currentColor" .../>
<path stroke="currentColor" .../>
```

**Unnecessary attributes — remove:**
- `id="..."` on internal elements
- `data-name="..."`
- empty `<g>` groups

**viewBox — make sure it exists:**
```xml
<svg viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg">
```

### Step 3 — Generate React components

Each icon → a separate `.tsx` file in `assets/icons/`:

```tsx
// assets/icons/ArrowRight.tsx
import React from "react";

export function ArrowRightIcon({
  size = 24,
  className,
  ...props
}: React.SVGProps<SVGSVGElement> & { size?: number }) {
  return (
    <svg
      width={size}
      height={size}
      viewBox="0 0 24 24"
      fill="none"
      aria-hidden="true"
      className={className}
      {...props}
    >
      <path d="..." stroke="currentColor" strokeWidth="2" strokeLinecap="round"/>
    </svg>
  );
}
```

Rules:
- `aria-hidden="true"` by default
- `size` prop instead of separate `width`/`height`
- `className` prop for Tailwind styling from outside (e.g. `<ArrowRightIcon className="text-primary" />`)
- Spread `...props` for flexibility
- File name: `PascalCase` + `Icon` suffix

### Step 4 — Barrel file

Generate `assets/icons/index.ts`:

```ts
export { ArrowRightIcon } from "./ArrowRight";
export { PlusIcon } from "./Plus";
// ... all icons
```

---

## Verification after Stage 3

- [ ] All SVGs use `currentColor`
- [ ] `viewBox` is defined everywhere
- [ ] Each icon has its own `.tsx` component
- [ ] `index.ts` exports all icons

Show the user the list of processed icons.
