# HTML Prototype Guide

**When to read:** During Step 4 (Path B), when building the `.html` output file for a full pattern with multiple states.
**Path A:** Use the template inside `simple-composite.md` instead.

**Contents:** [Template](#full-template) · [Token rules](#token-rules) · [Skeleton pattern](#skeleton) · [What to hardcode](#hardcode)

**Save output to:** `/patterns/{PatternName}.html`

> **Why CSS variables, not Tailwind classes:** This prototype runs in a plain browser with zero build tools. Tailwind CDN can't resolve custom classes from `tailwind.config.ts` (like `bg-brand-primary`). CSS variables from `tokens.css` work everywhere. The Tailwind class equivalents for production code are in spec.md → AI Agent Instructions.

---

## Full template (Path B) {#full-template}

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{PatternName} — Pattern Prototype</title>
  <link rel="stylesheet" href="../tokens/tokens.css">
  <style>
    *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
    body {
      font-family: var(--font-family-body);
      background: var(--colors-bg-primary);
      color: var(--colors-fg-primary);
    }
    .prototype-toolbar {
      display: flex;
      gap: 8px;
      padding: 8px 16px;
      background: #f5f5f5;
      border-bottom: 1px solid #ddd;
      position: sticky;
      top: 0;
      z-index: 100;
    }
    .prototype-toolbar button {
      padding: 4px 12px;
      border: 1px solid #ccc;
      border-radius: 6px;
      background: white;
      cursor: pointer;
      font-size: 12px;
    }
    .prototype-toolbar button.active {
      background: #333;
      color: white;
      border-color: transparent;
    }
    .state-panel { display: none; }
    .state-panel.active { display: block; }

    /* ── Pattern styles — tokens only ─────────────────────── */
    .pattern { }
    .pattern__header { }
    .pattern__content { }
    .pattern__footer { }
  </style>
</head>
<body>
  <nav class="prototype-toolbar">
    <button class="active" onclick="showState('loaded')">Loaded</button>
    <button onclick="showState('empty')">Empty</button>
    <button onclick="showState('loading')">Loading</button>
    <button onclick="showState('error')">Error</button>
  </nav>

  <div id="state-loaded" class="state-panel active">
    <div class="pattern">
      <!-- Loaded state — hardcoded sample data -->
    </div>
  </div>
  <div id="state-empty" class="state-panel">
    <div class="pattern"><!-- Empty state --></div>
  </div>
  <div id="state-loading" class="state-panel">
    <div class="pattern"><!-- Skeleton loading state --></div>
  </div>
  <div id="state-error" class="state-panel">
    <div class="pattern"><!-- Error state --></div>
  </div>

  <script>
    function showState(name) {
      document.querySelectorAll('.state-panel').forEach(el => el.classList.remove('active'));
      document.querySelectorAll('.prototype-toolbar button').forEach(el => el.classList.remove('active'));
      document.getElementById('state-' + name).classList.add('active');
      event.target.classList.add('active');
    }
  </script>
</body>
</html>
```

Add/remove state tabs to match the states from the spec. Only include states the pattern actually has.

---

## Token rules {#token-rules}

Every visual value in the pattern's custom styles must be a CSS variable — never hardcoded:

| Property | Token example |
|----------|--------------|
| color | `var(--colors-fg-primary)` |
| background | `var(--colors-bg-secondary)` |
| border | `1px solid var(--colors-border-primary)` |
| spacing | `var(--spacing-4)` |
| border-radius | `var(--radius-md)` |
| font-size | `var(--body-2-size)` |
| shadow | `var(--shadow-md)` |

If a token doesn't exist → use closest + comment: `/* closest available — check tokens.css */`

The prototype toolbar itself (`.prototype-toolbar`) uses hardcoded values intentionally — it's dev tooling, not part of the pattern.

**Zero defaults for container decoration:**
`box-shadow`, `border`, and `outline` on container elements default to **none**. Screenshots can't reliably show 1px borders or subtle shadows. Add only when confirmed by `get_design_context`.

---

## Skeleton / loading state {#skeleton}

```html
<div style="width:100%; height:var(--spacing-5); background:var(--colors-bg-tertiary);
  border-radius:var(--radius-sm); animation:skeleton-pulse 1.5s ease-in-out infinite;"></div>
```
```css
@keyframes skeleton-pulse { 0%,100% { opacity:1; } 50% { opacity:0.4; } }
```

In production Tailwind code, this becomes: `<div className="w-full h-5 bg-tertiary rounded-sm animate-pulse" />`

---

## What to hardcode (intentionally) {#hardcode}

Sample data is always hardcoded in the prototype — this is correct:
- Names, messages, timestamps, counts

Never hardcode: colors, spacing, typography, radius, shadows.
