# Preview Template Reference

HTML template for single-component preview pages.

Template variables:
- `{{COMPONENT_NAME}}` — PascalCase component name (e.g., `Button`)
- `{{COMPONENT_FILE}}` — relative path to .tsx file (e.g., `components/Button.tsx`)
- `{{TOKENS_PATH}}` — relative path to tokens CSS (e.g., `../tokens/tokens.css`)
- `{{DATE}}` — generation date (YYYY-MM-DD)
- `{{AUDIT_STATUS}}` — audit note or empty string
- `{{BG_TOKEN}}` — CSS custom property for page background (e.g., `var(--colors-bg-primary)`)
- `{{VARIANT_SECTIONS}}` — generated variant grid HTML
- `{{STATE_SECTIONS}}` — generated interaction states HTML
- `{{SPEC_SECTIONS}}` — generated design specs HTML

---

## Full HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Preview — {{COMPONENT_NAME}}</title>

  <!-- Design tokens for component rendering -->
  <link rel="stylesheet" href="{{TOKENS_PATH}}">

  <style>
    /* ============================================
       PREVIEW CHROME — system fonts, not DS fonts
       ============================================ */

    * {
      margin: 0;
      padding: 0;
      box-sizing: border-box;
    }

    body {
      background: {{BG_TOKEN}};
      padding: 40px;
      min-height: 100vh;
    }

    /* --- Header --- */

    .preview-header {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      margin-bottom: 48px;
    }

    .preview-header h1 {
      font-size: 24px;
      font-weight: 600;
      color: rgba(255, 255, 255, 0.9);
      margin-bottom: 8px;
    }

    .preview-header .preview-meta {
      font-size: 12px;
      color: rgba(255, 255, 255, 0.5);
      display: flex;
      gap: 16px;
      flex-wrap: wrap;
    }

    .preview-header .preview-audit {
      font-size: 12px;
      color: rgba(255, 200, 100, 0.8);
      margin-top: 4px;
    }

    /* --- Section headers --- */

    .section-title {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      font-size: 14px;
      font-weight: 600;
      color: rgba(255, 255, 255, 0.6);
      text-transform: uppercase;
      letter-spacing: 0.05em;
      margin-bottom: 20px;
      margin-top: 48px;
      padding-bottom: 8px;
      border-bottom: 1px solid rgba(255, 255, 255, 0.1);
    }

    /* --- Variant grid --- */

    .variant-grid {
      display: flex;
      flex-wrap: wrap;
      gap: 32px;
      margin-bottom: 40px;
    }

    .variant-cell {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      gap: 8px;
    }

    .variant-label {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      background: rgba(128, 128, 128, 0.3);
      color: #fff;
      padding: 2px 8px;
      border-radius: 4px;
      font-size: 11px;
      font-weight: 500;
      line-height: 16px;
      white-space: nowrap;
    }

    /* --- Variant matrix (size x variant) --- */

    .variant-matrix {
      width: 100%;
      border-collapse: separate;
      border-spacing: 24px 16px;
    }

    .variant-matrix th {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      font-size: 11px;
      font-weight: 500;
      color: rgba(255, 255, 255, 0.4);
      text-transform: uppercase;
      letter-spacing: 0.05em;
      text-align: left;
      padding-bottom: 8px;
    }

    .variant-matrix td {
      vertical-align: middle;
      padding: 8px 0;
    }

    /* --- Interaction states --- */

    .states-row {
      display: flex;
      flex-wrap: wrap;
      gap: 24px;
      align-items: flex-end;
      margin-bottom: 24px;
    }

    .state-cell {
      display: flex;
      flex-direction: column;
      align-items: flex-start;
      gap: 8px;
    }

    /* --- Design specs tables --- */

    .spec-block {
      margin-bottom: 32px;
    }

    .spec-block-title {
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      font-size: 13px;
      font-weight: 600;
      color: rgba(255, 255, 255, 0.7);
      margin-bottom: 12px;
    }

    .spec-table {
      width: 100%;
      max-width: 640px;
      border-collapse: collapse;
      font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto, sans-serif;
      font-size: 12px;
    }

    .spec-table th {
      text-align: left;
      font-weight: 500;
      color: rgba(255, 255, 255, 0.4);
      padding: 6px 16px 6px 0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.08);
      text-transform: uppercase;
      font-size: 10px;
      letter-spacing: 0.05em;
    }

    .spec-table td {
      padding: 6px 16px 6px 0;
      border-bottom: 1px solid rgba(255, 255, 255, 0.05);
      color: rgba(255, 255, 255, 0.7);
    }

    .spec-table td:first-child {
      color: rgba(255, 255, 255, 0.5);
      white-space: nowrap;
    }

    .spec-table td:nth-child(2) {
      font-family: "SF Mono", SFMono-Regular, Consolas, "Liberation Mono", Menlo, monospace;
      font-size: 11px;
    }

    .spec-table td:nth-child(3) {
      font-family: "SF Mono", SFMono-Regular, Consolas, "Liberation Mono", Menlo, monospace;
      font-size: 11px;
      color: rgba(255, 255, 255, 0.35);
    }

    .spec-group-label {
      font-size: 10px;
      font-weight: 600;
      text-transform: uppercase;
      letter-spacing: 0.08em;
      color: rgba(255, 255, 255, 0.3);
      padding: 12px 0 4px;
    }

    .spec-group-label td {
      border-bottom: none;
      padding-top: 16px;
    }

    /* --- Color swatch inline --- */

    .spec-color-swatch {
      display: inline-block;
      width: 12px;
      height: 12px;
      border-radius: 2px;
      vertical-align: middle;
      margin-right: 6px;
      border: 1px solid rgba(255, 255, 255, 0.1);
    }

    /* --- Theme-agnostic overrides ---
       The colors above assume white text on dark bg.
       If the DS uses a light background, override: */

    @media (prefers-color-scheme: light) {
      /* These only apply if BG_TOKEN resolves to a light color.
         In practice, read the actual token value and set
         appropriate text colors in the generated HTML. */
    }
  </style>
</head>
<body>

  <!-- ===== HEADER ===== -->
  <div class="preview-header">
    <h1>{{COMPONENT_NAME}}</h1>
    <div class="preview-meta">
      <span>{{COMPONENT_FILE}}</span>
      <span>Generated {{DATE}}</span>
    </div>
    {{AUDIT_STATUS}}
  </div>

  <!-- ===== VARIANT GRID ===== -->
  <div class="section-title">Variants</div>

  {{VARIANT_SECTIONS}}

  <!-- ===== INTERACTION STATES ===== -->
  <div class="section-title">Interaction States</div>

  {{STATE_SECTIONS}}

  <!-- ===== DESIGN SPECS ===== -->
  <div class="section-title">Design Specs</div>

  {{SPEC_SECTIONS}}

  <!-- ===== INTERACTIVITY ===== -->
  <script>
    {{INTERACTION_SCRIPT}}
  </script>

</body>
</html>
```

---

## Variant Grid — Single Axis Example

When the component has one variant axis (e.g., just `variant`):

```html
<div class="variant-grid">
  <div class="variant-cell">
    <!-- rendered component HTML -->
    <button class="...ds classes...">Primary</button>
    <span class="variant-label">primary</span>
  </div>
  <div class="variant-cell">
    <button class="...ds classes...">Secondary</button>
    <span class="variant-label">secondary</span>
  </div>
  <div class="variant-cell">
    <button class="...ds classes...">Tertiary</button>
    <span class="variant-label">tertiary</span>
  </div>
</div>
```

## Variant Matrix — Two Axes Example

When the component has two axes (e.g., `variant` x `size`):

```html
<table class="variant-matrix">
  <thead>
    <tr>
      <th></th>
      <th>sm</th>
      <th>md</th>
      <th>lg</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td><span class="variant-label">primary</span></td>
      <td>
        <button class="...ds classes for primary sm...">Button</button>
      </td>
      <td>
        <button class="...ds classes for primary md...">Button</button>
      </td>
      <td>
        <button class="...ds classes for primary lg...">Button</button>
      </td>
    </tr>
    <tr>
      <td><span class="variant-label">secondary</span></td>
      <td>
        <button class="...ds classes for secondary sm...">Button</button>
      </td>
      <td>
        <button class="...ds classes for secondary md...">Button</button>
      </td>
      <td>
        <button class="...ds classes for secondary lg...">Button</button>
      </td>
    </tr>
  </tbody>
</table>
```

---

## Interaction States Section

```html
<!-- Repeat for each variant -->
<div class="spec-block">
  <div class="spec-block-title">primary — States</div>
  <div class="states-row">
    <div class="state-cell">
      <button class="...ds classes...">Button</button>
      <span class="variant-label">default</span>
    </div>
    <div class="state-cell">
      <button class="...ds classes...">Button</button>
      <span class="variant-label">hover (see Figma)</span>
    </div>
    <div class="state-cell">
      <button class="...ds classes..." disabled>Button</button>
      <span class="variant-label">disabled</span>
    </div>
    <div class="state-cell">
      <button class="...ds classes...">
        <!-- spinner or loading indicator -->
        Loading...
      </button>
      <span class="variant-label">loading</span>
    </div>
  </div>
</div>
```

---

## Design Specs Table

One table per variant combination:

```html
<div class="spec-block">
  <div class="spec-block-title">primary / md — Design Specs</div>
  <table class="spec-table">
    <thead>
      <tr>
        <th>Tailwind class</th>
        <th>CSS variable</th>
        <th>Primitive value</th>
      </tr>
    </thead>
    <tbody>
      <!-- Spacing & Dimensions -->
      <tr class="spec-group-label">
        <td colspan="3">Spacing & Dimensions</td>
      </tr>
      <tr>
        <td>padding-x</td>
        <td>24px</td>
        <td>--spacing-6</td>
      </tr>
      <tr>
        <td>padding-y</td>
        <td>12px</td>
        <td>--spacing-3</td>
      </tr>
      <tr>
        <td>height</td>
        <td>44px</td>
        <td>--size-11</td>
      </tr>
      <tr>
        <td>gap</td>
        <td>8px</td>
        <td>--spacing-2</td>
      </tr>

      <!-- Colors -->
      <tr class="spec-group-label">
        <td colspan="3">Colors</td>
      </tr>
      <tr>
        <td>background</td>
        <td>
          <span class="spec-color-swatch" style="background:#6C5CE7"></span>
          #6C5CE7
        </td>
        <td>--colors-bg-brand</td>
      </tr>
      <tr>
        <td>text</td>
        <td>
          <span class="spec-color-swatch" style="background:#FFFFFF"></span>
          #FFFFFF
        </td>
        <td>--colors-text-inverse</td>
      </tr>
      <tr>
        <td>border</td>
        <td>none</td>
        <td>—</td>
      </tr>

      <!-- Typography -->
      <tr class="spec-group-label">
        <td colspan="3">Typography</td>
      </tr>
      <tr>
        <td>font-size</td>
        <td>14px</td>
        <td>--font-size-sm</td>
      </tr>
      <tr>
        <td>line-height</td>
        <td>20px</td>
        <td>--line-height-sm</td>
      </tr>
      <tr>
        <td>font-weight</td>
        <td>500</td>
        <td>--font-weight-medium</td>
      </tr>

      <!-- Border -->
      <tr class="spec-group-label">
        <td colspan="3">Border</td>
      </tr>
      <tr>
        <td>border-radius</td>
        <td>8px</td>
        <td>--radius-md</td>
      </tr>
      <tr>
        <td>border-width</td>
        <td>0px</td>
        <td>—</td>
      </tr>
    </tbody>
  </table>
</div>
```

---

## Audit Status (optional)

If an audit file exists with open findings:

```html
<div class="preview-audit">Audit: 2 warnings, 1 token gap open — see audits/Button.audit.md</div>
```

If no audit file exists, omit this element entirely.

---

## Theme Adaptation

The template uses `rgba(255, 255, 255, ...)` for chrome text, which works on dark backgrounds. When generating for a light-themed DS:

- Swap chrome text colors to `rgba(0, 0, 0, ...)` equivalents
- Label pills remain `rgba(128, 128, 128, 0.3)` (works on both light and dark)
- Read the actual resolved value of the background token to decide which text color set to use
