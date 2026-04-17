# CONTRIBUTING.md — Structure & Rules

Who reads this: a developer adding a new component, or syncing tokens from Figma.
Tone: step-by-step, no ambiguity.

## Required sections

### 1. Adding a component

Numbered steps, very literal:

1. Create `src/components/[Name].tsx`
2. Copy the file header comment block from an existing component — fill in Figma node ID
3. Define `[Name]Props` interface, extend the relevant HTML element attributes
4. Export: named export + `export default`
5. Add to `src/components/index.ts` barrel export
6. Add variant rows to `index.html` showcase (see "Adding to showcase" below)
7. Update STATUS.md — change status to ✅ Done

### 2. Token sync from Figma

How to re-export tokens when Figma variables change:
1. Open Figma file → Variables panel → Export as CSS
2. Replace `src/tokens/tokens.css` entirely — do not merge manually
3. Verify no existing component references a removed token (grep for old var name)
4. Update export date in README.md and CHANGELOG.md

### 3. Adding to showcase (index.html)

Structure of a variant row — show the actual HTML pattern used in the project:
```html
<div class="variant-group">
  <div class="variant-group-label">States</div>
  <div class="variant-row">
    <div class="variant-cell">
      <!-- component HTML -->
      <div class="variant-label">Default</div>
    </div>
  </div>
</div>
```

### 4. Figma ↔ Component parity check

Before marking a component Done:
- [ ] All Figma variants covered (check variant axes in component header comment)
- [ ] All tokens used are from `tokens.css`, no hardcoded values
- [ ] `forwardRef` used if component wraps an input element
- [ ] `aria-*` attributes present for interactive states
- [ ] Component renders in `index.html` showcase

### 5. Versioning convention

This project uses manual versioning in CHANGELOG.md, not npm semver.
Format: `YYYY-MM-DD — Component Name — what changed`
