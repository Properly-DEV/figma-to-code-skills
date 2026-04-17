# Foundation Page Template

HTML template for foundation/token pages (colors, typography, spacing, radius, shadows). Uses the same three-panel layout as component pages but with simpler center content — no playground controls, no inspector.

## Template Variables

| Variable | Source | Example |
|---|---|---|
| `{{PROJECT_NAME}}` | CLAUDE.md project name | Acme DS |
| `{{FOUNDATION_NAME}}` | Page title | Colors |
| `{{FOUNDATION_SLUG}}` | Lowercase | colors |
| `{{NAV_ITEMS}}` | Full navigation tree HTML | `<li>...` |
| `{{FOUNDATION_CONTENT}}` | Main content HTML | varies by type |
| `{{TOKENS_CSS_PATH}}` | Relative path to tokens.css | ../tokens/tokens.css |

## Base HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{FOUNDATION_NAME}} — {{PROJECT_NAME}} Design System</title>
  <link rel="stylesheet" href="../css/showcase.css">
  <link rel="stylesheet" href="{{TOKENS_CSS_PATH}}">
</head>
<body class="sc-body">
  <div class="sc-layout sc-layout--two-panel">

    <!-- LEFT SIDEBAR -->
    <aside class="sc-sidebar">
      <div class="sc-sidebar-header">
        <h1 class="sc-project-name">{{PROJECT_NAME}}</h1>
        <span class="sc-subtitle">Design System</span>
      </div>

      <nav class="sc-nav">
        <div class="sc-nav-group">
          <button class="sc-nav-group-toggle" data-group="foundations">Foundations</button>
          <ul class="sc-nav-list" data-group-list="foundations">
            <li><a href="colors.html">Colors</a></li>
            <li><a href="typography.html">Typography</a></li>
            <li><a href="spacing.html">Spacing</a></li>
            <li><a href="radius.html">Radius</a></li>
            <li><a href="shadows.html">Shadows</a></li>
          </ul>
        </div>
        <div class="sc-nav-group">
          <button class="sc-nav-group-toggle" data-group="components">Components</button>
          <ul class="sc-nav-list" data-group-list="components">
            {{NAV_ITEMS}}
          </ul>
        </div>
      </nav>
    </aside>

    <!-- CENTER PANEL (full width, no right inspector) -->
    <main class="sc-main sc-main--wide">
      <div class="sc-main-header">
        <h2 class="sc-component-title">{{FOUNDATION_NAME}}</h2>
        <span class="sc-component-meta">tokens/tokens.css</span>
      </div>

      <div class="sc-foundation-content">
        {{FOUNDATION_CONTENT}}
      </div>
    </main>
  </div>

  <script src="../js/showcase.js"></script>
</body>
</html>
```

Note: Foundation pages use `.sc-layout--two-panel` which sets `grid-template-columns: 260px 1fr` (no right panel).

---

## Colors Page Content

Read all `--color-*` and `--colors-*` CSS custom properties from `tokens/tokens.css`. Group them logically (background, text, border, brand, semantic, etc.) based on the token naming convention.

```html
<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Background Colors</h3>
  <div class="sc-color-grid">

    <div class="sc-color-swatch-card">
      <div class="sc-color-swatch-preview" style="background-color: var(--colors-bg-primary)"></div>
      <div class="sc-color-swatch-info">
        <span class="sc-color-swatch-name">Primary Background</span>
        <span class="sc-color-swatch-hex">#0D0D0D</span>
        <span class="sc-color-swatch-token">--colors-bg-primary</span>
      </div>
    </div>

    <div class="sc-color-swatch-card">
      <div class="sc-color-swatch-preview" style="background-color: var(--colors-bg-secondary)"></div>
      <div class="sc-color-swatch-info">
        <span class="sc-color-swatch-name">Secondary Background</span>
        <span class="sc-color-swatch-hex">#1A1A1A</span>
        <span class="sc-color-swatch-token">--colors-bg-secondary</span>
      </div>
    </div>

    <!-- ... more swatches -->
  </div>
</div>

<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Text Colors</h3>
  <div class="sc-color-grid">
    <!-- ... text color swatches -->
  </div>
</div>
```

### CSS for color grid
- `.sc-color-grid` — display:grid, grid-template-columns: repeat(auto-fill, minmax(180px, 1fr)), gap:16px
- `.sc-color-swatch-card` — background:#1a1a1a, border-radius:8px, overflow:hidden
- `.sc-color-swatch-preview` — height:80px, width:100%
- `.sc-color-swatch-info` — padding:12px, display:flex, flex-direction:column, gap:2px
- `.sc-color-swatch-name` — color:#ccc, font-size:13px, font-weight:500
- `.sc-color-swatch-hex` — color:#888, font-size:12px, font-family:monospace
- `.sc-color-swatch-token` — color:#555, font-size:11px, font-family:monospace

For very light or white swatches, add a subtle border: `border: 1px solid #333` on the preview div.

---

## Typography Page Content

Read all typography-related tokens (font-size, line-height, font-weight, letter-spacing, font-family). Group by scale (display, heading, body, label, caption).

```html
<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Headings</h3>

  <div class="sc-type-specimen">
    <div class="sc-type-preview" style="font-size: var(--heading-1-size); line-height: var(--heading-1-line-height); font-weight: 500;">
      The quick brown fox jumps over the lazy dog
    </div>
    <div class="sc-type-meta">
      <span class="sc-type-name">Heading 1</span>
      <span class="sc-type-details">56px / 64px -- Medium (500)</span>
      <span class="sc-type-token">--heading-1-size, --heading-1-line-height</span>
    </div>
  </div>

  <div class="sc-type-specimen">
    <div class="sc-type-preview" style="font-size: var(--heading-2-size); line-height: var(--heading-2-line-height); font-weight: 500;">
      The quick brown fox jumps over the lazy dog
    </div>
    <div class="sc-type-meta">
      <span class="sc-type-name">Heading 2</span>
      <span class="sc-type-details">40px / 48px -- Medium (500)</span>
      <span class="sc-type-token">--heading-2-size, --heading-2-line-height</span>
    </div>
  </div>

  <!-- ... more specimens -->
</div>

<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Body</h3>
  <!-- ... body type specimens -->
</div>
```

### Text content rules
- Display sizes (very large): use "Aa" only
- Headings: use "The quick brown fox jumps over the lazy dog"
- Body: use "The quick brown fox jumps over the lazy dog. Pack my box with five dozen liquor jugs."
- Button/Label styles: use "Button Label" or "LABEL TEXT" with appropriate casing
- Caption: use "Caption text with smaller size"

### CSS for type specimens
- `.sc-type-specimen` — padding:24px 0, border-bottom:1px solid #1a1a1a
- `.sc-type-preview` — color:#fff, margin-bottom:8px (inherits font from DS tokens, displayed inside token-loaded area)
- `.sc-type-meta` — display:flex, gap:16px, align-items:baseline
- `.sc-type-name` — color:#ccc, font-size:13px, font-weight:500, min-width:100px
- `.sc-type-details` — color:#888, font-size:12px, font-family:monospace
- `.sc-type-token` — color:#555, font-size:11px, font-family:monospace

---

## Spacing Page Content

Read all `--spacing-*` tokens. Display as horizontal bars with labeled values.

```html
<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Spacing Scale</h3>

  <div class="sc-spacing-list">
    <div class="sc-spacing-item">
      <span class="sc-spacing-label">--spacing-1</span>
      <div class="sc-spacing-bar" style="width: var(--spacing-1)"></div>
      <span class="sc-spacing-value">4px</span>
    </div>

    <div class="sc-spacing-item">
      <span class="sc-spacing-label">--spacing-2</span>
      <div class="sc-spacing-bar" style="width: var(--spacing-2)"></div>
      <span class="sc-spacing-value">8px</span>
    </div>

    <div class="sc-spacing-item">
      <span class="sc-spacing-label">--spacing-4</span>
      <div class="sc-spacing-bar" style="width: var(--spacing-4)"></div>
      <span class="sc-spacing-value">16px</span>
    </div>

    <!-- ... more spacing tokens -->
  </div>
</div>
```

### CSS for spacing
- `.sc-spacing-list` — display:flex, flex-direction:column, gap:8px
- `.sc-spacing-item` — display:grid, grid-template-columns:140px 1fr 60px, align-items:center, gap:12px
- `.sc-spacing-label` — color:#888, font-size:12px, font-family:monospace, text-align:right
- `.sc-spacing-bar` — height:24px, background:#4CAF50, border-radius:4px, min-width:4px
- `.sc-spacing-value` — color:#ccc, font-size:12px, font-family:monospace

---

## Radius Page Content

Read all `--radius-*` tokens. Show boxes with each border-radius applied.

```html
<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Border Radius</h3>

  <div class="sc-radius-grid">
    <div class="sc-radius-card">
      <div class="sc-radius-preview" style="border-radius: var(--radius-sm)"></div>
      <div class="sc-radius-info">
        <span class="sc-radius-name">Small</span>
        <span class="sc-radius-value">4px</span>
        <span class="sc-radius-token">--radius-sm</span>
      </div>
    </div>

    <div class="sc-radius-card">
      <div class="sc-radius-preview" style="border-radius: var(--radius-md)"></div>
      <div class="sc-radius-info">
        <span class="sc-radius-name">Medium</span>
        <span class="sc-radius-value">8px</span>
        <span class="sc-radius-token">--radius-md</span>
      </div>
    </div>

    <div class="sc-radius-card">
      <div class="sc-radius-preview" style="border-radius: var(--radius-lg)"></div>
      <div class="sc-radius-info">
        <span class="sc-radius-name">Large</span>
        <span class="sc-radius-value">12px</span>
        <span class="sc-radius-token">--radius-lg</span>
      </div>
    </div>

    <div class="sc-radius-card">
      <div class="sc-radius-preview" style="border-radius: var(--radius-full)"></div>
      <div class="sc-radius-info">
        <span class="sc-radius-name">Full</span>
        <span class="sc-radius-value">9999px</span>
        <span class="sc-radius-token">--radius-full</span>
      </div>
    </div>
  </div>
</div>
```

### CSS for radius
- `.sc-radius-grid` — display:grid, grid-template-columns: repeat(auto-fill, minmax(160px, 1fr)), gap:24px
- `.sc-radius-card` — text-align:center
- `.sc-radius-preview` — width:100px, height:100px, background:#333, border:2px solid #555, margin:0 auto 12px
- `.sc-radius-info` — display:flex, flex-direction:column, gap:2px
- `.sc-radius-name` — color:#ccc, font-size:13px, font-weight:500
- `.sc-radius-value` — color:#888, font-size:12px, font-family:monospace
- `.sc-radius-token` — color:#555, font-size:11px, font-family:monospace

---

## Shadows Page Content

Read all `--shadow-*` tokens. Show boxes with each shadow applied.

```html
<div class="sc-foundation-section">
  <h3 class="sc-foundation-section-title">Shadows</h3>

  <div class="sc-shadow-grid">
    <div class="sc-shadow-card">
      <div class="sc-shadow-preview" style="box-shadow: var(--shadow-sm)"></div>
      <div class="sc-shadow-info">
        <span class="sc-shadow-name">Small</span>
        <span class="sc-shadow-value">0 1px 2px rgba(0,0,0,0.1)</span>
        <span class="sc-shadow-token">--shadow-sm</span>
      </div>
    </div>

    <div class="sc-shadow-card">
      <div class="sc-shadow-preview" style="box-shadow: var(--shadow-md)"></div>
      <div class="sc-shadow-info">
        <span class="sc-shadow-name">Medium</span>
        <span class="sc-shadow-value">0 2px 8px + 0 4px 14px</span>
        <span class="sc-shadow-token">--shadow-md</span>
      </div>
    </div>

    <div class="sc-shadow-card">
      <div class="sc-shadow-preview" style="box-shadow: var(--shadow-lg)"></div>
      <div class="sc-shadow-info">
        <span class="sc-shadow-name">Large</span>
        <span class="sc-shadow-value">0 4px 16px + 0 8px 32px</span>
        <span class="sc-shadow-token">--shadow-lg</span>
      </div>
    </div>
  </div>
</div>
```

### CSS for shadows
- `.sc-shadow-grid` — display:grid, grid-template-columns: repeat(auto-fill, minmax(200px, 1fr)), gap:32px
- `.sc-shadow-card` — text-align:center
- `.sc-shadow-preview` — width:120px, height:120px, background:#1a1a1a, border-radius:8px, margin:0 auto 16px
- `.sc-shadow-info` — display:flex, flex-direction:column, gap:2px
- `.sc-shadow-name` — color:#ccc, font-size:13px, font-weight:500
- `.sc-shadow-value` — color:#888, font-size:11px, font-family:monospace
- `.sc-shadow-token` — color:#555, font-size:11px, font-family:monospace

---

## How to Extract Tokens from tokens.css

Read `tokens/tokens.css` and parse the `:root` block. For each `--property: value;` line:

1. Categorize by prefix:
   - `--color-*`, `--colors-*` → Colors page
   - `--font-*`, `--heading-*`, `--body-*`, `--label-*`, `--caption-*` → Typography page
   - `--spacing-*` → Spacing page
   - `--radius-*` → Radius page
   - `--shadow-*` → Shadows page

2. Extract the raw value (hex, px, rem, etc.) for display alongside the token name

3. Group tokens within each category by their second-level prefix (e.g., `--colors-bg-*` = Background, `--colors-text-*` = Text)

4. Generate human-readable names from token names:
   - `--colors-bg-primary` → "Primary Background"
   - `--spacing-4` → "4 (16px)"
   - `--radius-md` → "Medium"
