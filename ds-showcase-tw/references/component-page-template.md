# Component Page Template

Full HTML template for a single component's showcase page. The agent populates template variables and copies CSS/JS verbatim.

## Template Variables

| Variable | Source | Example |
|---|---|---|
| `{{PROJECT_NAME}}` | CLAUDE.md project name | Eterna |
| `{{COMPONENT_NAME}}` | Component file name (PascalCase) | Button |
| `{{COMPONENT_SLUG}}` | Lowercase kebab-case | button |
| `{{COMPONENT_FILE}}` | Source file path | components/Button.tsx |
| `{{FIGMA_NODE}}` | From header comment in .tsx | 123:456 |
| `{{NAV_ITEMS}}` | Full navigation tree HTML | `<li>...` |
| `{{PROPS_CONTROLS}}` | Generated playground controls HTML | `<div class="sc-control">...` |
| `{{PREVIEW_HTML}}` | Default rendered component HTML | `<button class="...">` |
| `{{VARIANTS_HTML}}` | Variant matrix grid HTML | `<div class="sc-variant-grid">...` |
| `{{ISSUES_HTML}}` | Audit findings HTML | `<div class="sc-issue">...` |
| `{{INSPECTOR_DESIGN}}` | Design tab token rows HTML | `<tr>...` |
| `{{INSPECTOR_CODE}}` | JSX code snippet | `<Button variant="primary">` |
| `{{INSPECTOR_PROPS}}` | Props API table rows HTML | `<tr>...` |
| `{{TOKENS_CSS_PATH}}` | Relative path to tokens.css | ../tokens/tokens.css |
| `{{ENV_CONTROLS}}` | Environment controls HTML (filtered) | `<div class="sc-env-control">...` |
| `{{CURRENT_SELECTION}}` | Default selection label | Button -- primary -- md |

## HTML Template

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>{{COMPONENT_NAME}} — {{PROJECT_NAME}} Design System</title>
  <link rel="stylesheet" href="../css/showcase.css">
</head>
<body class="sc-body">
  <div class="sc-layout">

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
            <li><a href="../foundations/colors.html">Colors</a></li>
            <li><a href="../foundations/typography.html">Typography</a></li>
            <li><a href="../foundations/spacing.html">Spacing</a></li>
            <li><a href="../foundations/radius.html">Radius</a></li>
            <li><a href="../foundations/shadows.html">Shadows</a></li>
          </ul>
        </div>
        <div class="sc-nav-group">
          <button class="sc-nav-group-toggle" data-group="components">Components</button>
          <ul class="sc-nav-list" data-group-list="components">
            {{NAV_ITEMS}}
          </ul>
        </div>
      </nav>

      <div class="sc-env-controls">
        <h3 class="sc-env-title">Environment</h3>
        {{ENV_CONTROLS}}
      </div>
    </aside>

    <!-- CENTER PANEL -->
    <main class="sc-main">
      <div class="sc-main-header">
        <h2 class="sc-component-title">{{COMPONENT_NAME}}</h2>
        <span class="sc-component-meta">{{COMPONENT_FILE}}</span>
      </div>

      <div class="sc-tabs">
        <button class="sc-tab sc-tab--active" data-tab="playground">Playground</button>
        <button class="sc-tab" data-tab="variants">All Variants</button>
        <button class="sc-tab" data-tab="issues">Issues</button>
      </div>

      <!-- PLAYGROUND TAB -->
      <div class="sc-tab-content sc-tab-content--active" data-tab-content="playground">
        <div class="sc-playground">
          <div class="sc-controls-bar">
            {{PROPS_CONTROLS}}
          </div>
          <div class="sc-preview-wrapper">
            <div class="sc-preview" id="sc-preview">
              <link rel="stylesheet" href="{{TOKENS_CSS_PATH}}">
              {{PREVIEW_HTML}}
            </div>
            <div class="sc-resize-handle" id="sc-resize-handle"></div>
          </div>
        </div>
      </div>

      <!-- ALL VARIANTS TAB -->
      <div class="sc-tab-content" data-tab-content="variants">
        {{VARIANTS_HTML}}
      </div>

      <!-- ISSUES TAB -->
      <div class="sc-tab-content" data-tab-content="issues">
        {{ISSUES_HTML}}
      </div>
    </main>

    <!-- RIGHT PANEL — INSPECTOR -->
    <aside class="sc-inspector">
      <div class="sc-inspector-header">
        <span class="sc-selection-label" id="sc-selection-label">{{CURRENT_SELECTION}}</span>
      </div>

      <div class="sc-tabs sc-tabs--inspector">
        <button class="sc-tab sc-tab--active" data-inspector-tab="design">Design</button>
        <button class="sc-tab" data-inspector-tab="code">Code</button>
        <button class="sc-tab" data-inspector-tab="props">Props</button>
      </div>

      <!-- DESIGN TAB -->
      <div class="sc-inspector-content sc-inspector-content--active" data-inspector-content="design">
        <div class="sc-inspector-group">
          <h4 class="sc-inspector-group-title">Spacing & Dimensions</h4>
          <table class="sc-inspector-table" id="sc-design-spacing">
            {{INSPECTOR_DESIGN}}
          </table>
        </div>
        <div class="sc-inspector-group">
          <h4 class="sc-inspector-group-title">Colors</h4>
          <table class="sc-inspector-table" id="sc-design-colors">
          </table>
        </div>
        <div class="sc-inspector-group">
          <h4 class="sc-inspector-group-title">Typography</h4>
          <table class="sc-inspector-table" id="sc-design-typography">
          </table>
        </div>
        <div class="sc-inspector-group">
          <h4 class="sc-inspector-group-title">Border</h4>
          <table class="sc-inspector-table" id="sc-design-border">
          </table>
        </div>
        <div class="sc-inspector-group">
          <h4 class="sc-inspector-group-title">States</h4>
          <table class="sc-inspector-table" id="sc-design-states">
          </table>
        </div>
      </div>

      <!-- CODE TAB -->
      <div class="sc-inspector-content" data-inspector-content="code">
        <div class="sc-code-block">
          <div class="sc-code-header">
            <span>JSX</span>
            <button class="sc-copy-btn" id="sc-copy-code">Copy</button>
          </div>
          <pre class="sc-code"><code id="sc-code-output">{{INSPECTOR_CODE}}</code></pre>
        </div>
        <div class="sc-inspector-group">
          <h4 class="sc-inspector-group-title">Tokens Used</h4>
          <ul class="sc-token-list" id="sc-tokens-used">
          </ul>
        </div>
      </div>

      <!-- PROPS TAB -->
      <div class="sc-inspector-content" data-inspector-content="props">
        <table class="sc-props-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Type</th>
              <th>Default</th>
              <th>Description</th>
            </tr>
          </thead>
          <tbody>
            {{INSPECTOR_PROPS}}
          </tbody>
        </table>
      </div>
    </aside>
  </div>

  <script src="../js/showcase.js"></script>
  <script>
    // Component-specific data — generated by the skill
    window.SC_COMPONENT = {
      name: '{{COMPONENT_NAME}}',
      slug: '{{COMPONENT_SLUG}}',
      variants: {
        // Populated from cva() extraction
        // e.g. variant: ['primary', 'secondary', 'tertiary'],
        //      size: ['sm', 'md', 'lg']
      },
      defaults: {
        // e.g. variant: 'primary', size: 'md'
      },
      props: [
        // { name: 'variant', type: 'enum', values: [...], default: '...' },
        // { name: 'disabled', type: 'boolean', default: false },
        // { name: 'children', type: 'string', default: 'Button' },
      ],
      tokens: {
        // Token mappings per variant combination
        // 'primary-md': { bg: '--color-primary', text: '--color-on-primary', ... }
      },
      renderPreview: function(props) {
        // Returns HTML string for the current prop combination
        // Generated from component's rendered output structure
      }
    };

    // Initialize the playground
    SC.initPlayground();
  </script>
</body>
</html>
```

## Generating Playground Controls

For each prop extracted from the component's TypeScript interface, generate the appropriate control:

### Enum props (variant, size, state, type)

```html
<div class="sc-control">
  <label class="sc-control-label">variant</label>
  <select class="sc-control-select" data-prop="variant">
    <option value="primary" selected>primary</option>
    <option value="secondary">secondary</option>
    <option value="tertiary">tertiary</option>
  </select>
</div>
```

Source: extract from `cva()` variants object. Each key is a prop name, each value array becomes options.

### Boolean props (disabled, loading, clearable, isFilled)

```html
<div class="sc-control">
  <label class="sc-control-label">disabled</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-prop="disabled">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>
```

Source: any prop typed as `boolean` in the interface.

### String props (children, placeholder, label)

```html
<div class="sc-control">
  <label class="sc-control-label">children</label>
  <input type="text" class="sc-control-input" data-prop="children" value="Button">
</div>
```

Source: any prop typed as `string` in the interface. Use default value or a sensible placeholder.

### Slot props (startAddon, endAddon, icon, leftIcon)

```html
<div class="sc-control">
  <label class="sc-control-label">startAddon</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-prop="startAddon" data-slot="true">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>
<!-- Nested controls appear when slot is enabled -->
<div class="sc-slot-controls" data-slot-parent="startAddon" style="display:none">
  <div class="sc-control sc-control--nested">
    <label class="sc-control-label">addon icon</label>
    <select class="sc-control-select" data-prop="startAddon.icon">
      <option value="search">Search</option>
      <option value="mail">Mail</option>
      <option value="user">User</option>
    </select>
  </div>
</div>
```

Source: any prop typed as `ReactNode` or `React.ReactNode` in the interface.

## Wiring JS Controls to Preview

Every control has a `data-prop` attribute. The `showcase.js` `initPlayground()` function:

1. Reads all `[data-prop]` elements on the page
2. Attaches `change`/`input` event listeners
3. On change: collects all current prop values into an object
4. Calls `SC_COMPONENT.renderPreview(props)` to get new HTML
5. Replaces `#sc-preview` innerHTML with the result
6. Updates `#sc-selection-label` with current selection string
7. Updates `#sc-code-output` with the JSX representation
8. Updates inspector Design tab values by reading token mappings from `SC_COMPONENT.tokens`

### Slot toggle behavior

When a slot toggle (`data-slot="true"`) is checked:
1. Show the corresponding `[data-slot-parent]` container
2. Include the slot in the rendered preview
3. When unchecked: hide nested controls, remove slot from preview

## Generating the Variant Matrix

For the "All Variants" tab, generate a grid of every variant x size combination:

```html
<div class="sc-variant-matrix">
  <table class="sc-variant-table">
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
        <td class="sc-variant-row-label">primary</td>
        <td class="sc-variant-cell">
          <div class="sc-variant-preview">
            <link rel="stylesheet" href="{{TOKENS_CSS_PATH}}">
            <!-- rendered component HTML: primary + sm -->
          </div>
          <span class="sc-variant-cell-label">primary / sm</span>
        </td>
        <!-- ... more size columns -->
      </tr>
      <!-- ... more variant rows -->
    </tbody>
  </table>
</div>
```

If the component has more than 2 variant axes, nest the less important ones as rows within each primary variant group.

## Generating Issues Tab

Read `_designer/audits/{ComponentName}.audit.md` and parse findings:

```html
<div class="sc-issues-list">
  <div class="sc-issue">
    <span class="sc-issue-badge sc-issue-badge--critical">Critical</span>
    <span class="sc-issue-text">Focus ring missing on keyboard navigation</span>
  </div>
  <div class="sc-issue">
    <span class="sc-issue-badge sc-issue-badge--minor">Minor</span>
    <span class="sc-issue-text">Hover color slightly off from Figma spec</span>
  </div>
</div>
```

If no audit file exists:

```html
<div class="sc-issues-empty">
  <p>No audit data available for this component.</p>
</div>
```

## Generating Inspector Design Tab

Map the component's cva classes to their underlying CSS custom properties. For each variant combination, list the computed token values:

```html
<tr>
  <td class="sc-inspector-prop">background</td>
  <td class="sc-inspector-value">
    <span class="sc-color-swatch" style="background:#6C5CE7"></span>
    #6C5CE7
  </td>
  <td class="sc-inspector-token">--color-primary</td>
</tr>
<tr>
  <td class="sc-inspector-prop">padding</td>
  <td class="sc-inspector-value">12px 24px</td>
  <td class="sc-inspector-token">--spacing-3 --spacing-6</td>
</tr>
```

Values must update when playground controls change. The JS reads from `SC_COMPONENT.tokens[currentKey]`.

## Generating Inspector Code Tab

Build JSX from current props:

```jsx
<Button variant="primary" size="md" disabled>
  Click me
</Button>
```

Only include props that differ from defaults. Always include `children` as the inner content.

## Generating Inspector Props Tab

```html
<tr>
  <td class="sc-props-name">variant<span class="sc-props-required">*</span></td>
  <td class="sc-props-type">"primary" | "secondary" | "tertiary"</td>
  <td class="sc-props-default">"primary"</td>
  <td class="sc-props-desc">Visual style variant</td>
</tr>
```

## Environment Controls Generation

Only render controls that are relevant to the current component:

```html
<!-- Always show -->
<div class="sc-env-control">
  <label class="sc-control-label">Viewport</label>
  <div class="sc-viewport-presets">
    <button class="sc-viewport-btn" data-width="320">320</button>
    <button class="sc-viewport-btn" data-width="768">768</button>
    <button class="sc-viewport-btn" data-width="1024">1024</button>
    <button class="sc-viewport-btn sc-viewport-btn--active" data-width="full">Full</button>
  </div>
</div>

<!-- Only if component has disabled prop -->
<div class="sc-env-control" data-requires-prop="disabled">
  <label class="sc-control-label">Force disabled</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-env="force-disabled">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>

<!-- Only if component has loading prop -->
<div class="sc-env-control" data-requires-prop="loading">
  <label class="sc-control-label">Force loading</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-env="force-loading">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>

<!-- Always show -->
<div class="sc-env-control">
  <label class="sc-control-label">Show focus rings</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-env="show-focus">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>

<!-- Always show -->
<div class="sc-env-control">
  <label class="sc-control-label">RTL mode</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-env="rtl">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>

<!-- Always show -->
<div class="sc-env-control">
  <label class="sc-control-label">Text overflow</label>
  <label class="sc-control-toggle">
    <input type="checkbox" data-env="text-overflow">
    <span class="sc-control-toggle-track"></span>
  </label>
</div>
```

## CSS for showcase chrome

All showcase CSS goes in `showcase/css/showcase.css`. It uses the `sc-` prefix exclusively. The full CSS is generated by the skill based on these specifications:

### Layout
- `.sc-body` — margin:0, height:100vh, overflow:hidden, font-family: -apple-system,BlinkMacSystemFont,"Segoe UI",Roboto,sans-serif
- `.sc-layout` — display:grid, grid-template-columns: 260px 1fr 320px, height:100vh
- `.sc-sidebar` — background:#111, color:#999, overflow-y:auto, padding:16px, border-right:1px solid #222
- `.sc-main` — background:#0a0a0a, overflow-y:auto, padding:24px
- `.sc-inspector` — background:#111, color:#999, overflow-y:auto, padding:16px, border-left:1px solid #222

### Navigation
- `.sc-nav-group-toggle` — full-width button, text-align:left, color:#ccc, padding:8px 0, background:none, border:none, cursor:pointer, font-weight:600
- `.sc-nav-list` — list-style:none, padding-left:12px, margin:0
- `.sc-nav-list a` — color:#888, text-decoration:none, display:block, padding:4px 8px, border-radius:4px, font-size:13px
- `.sc-nav-list a:hover` — color:#fff, background:#1a1a1a
- `.sc-nav-list a.sc-nav--active` — color:#fff, background:#1e1e1e

### Tabs
- `.sc-tabs` — display:flex, gap:0, border-bottom:1px solid #222
- `.sc-tab` — padding:8px 16px, background:none, border:none, color:#666, cursor:pointer, font-size:13px, border-bottom:2px solid transparent
- `.sc-tab--active` — color:#fff, border-bottom-color:#fff

### Controls
- `.sc-controls-bar` — display:flex, flex-wrap:wrap, gap:12px, padding:12px 0, border-bottom:1px solid #222
- `.sc-control` — display:flex, align-items:center, gap:8px
- `.sc-control-label` — font-size:12px, color:#888, min-width:60px, text-transform:uppercase, letter-spacing:0.05em
- `.sc-control-select` — background:#1a1a1a, color:#ccc, border:1px solid #333, border-radius:4px, padding:4px 8px, font-size:13px
- `.sc-control-input` — same as select
- `.sc-control-toggle-track` — 32px x 18px, border-radius:9px, background:#333, transition:background 0.2s
- checked: background:#4CAF50

### Preview
- `.sc-preview-wrapper` — position:relative, flex:1, min-height:200px
- `.sc-preview` — padding:32px, display:flex, align-items:center, justify-content:center, background:#0d0d0d, border:1px solid #222, border-radius:8px, min-height:120px
- `.sc-resize-handle` — position:absolute, right:0, top:0, bottom:0, width:6px, cursor:ew-resize, background:#333 on hover

### Inspector
- `.sc-inspector-table` — width:100%, font-size:12px, border-collapse:collapse
- `.sc-inspector-table td` — padding:4px 0, border-bottom:1px solid #1a1a1a
- `.sc-inspector-prop` — color:#888
- `.sc-inspector-value` — color:#ccc, font-family:monospace
- `.sc-inspector-token` — color:#555, font-size:11px
- `.sc-color-swatch` — inline-block, 12px x 12px, border-radius:2px, vertical-align:middle, margin-right:4px

### Issues
- `.sc-issue-badge` — padding:2px 8px, border-radius:3px, font-size:11px, text-transform:uppercase, font-weight:600
- `.sc-issue-badge--critical` — background:#ff4444, color:#fff
- `.sc-issue-badge--major` — background:#ff8800, color:#fff
- `.sc-issue-badge--minor` — background:#ffcc00, color:#000
- `.sc-issue-badge--suggestion` — background:#4488ff, color:#fff

## JS for showcase interactivity

All JS goes in `showcase/js/showcase.js`. Key functions:

### SC.initPlayground()
- Reads `window.SC_COMPONENT` data
- Attaches listeners to all `[data-prop]` controls
- Calls `updatePreview()` on any change

### SC.updatePreview()
- Collects all current prop values from controls
- Calls `SC_COMPONENT.renderPreview(props)` for new HTML
- Updates `#sc-preview` innerHTML
- Updates `#sc-selection-label` text
- Updates `#sc-code-output` with JSX
- Calls `updateInspector(props)`

### SC.updateInspector(props)
- Builds a key from current variant+size (e.g. "primary-md")
- Reads `SC_COMPONENT.tokens[key]`
- Populates inspector Design tab tables

### Tab switching
- Main tabs: `[data-tab]` buttons toggle `[data-tab-content]` visibility
- Inspector tabs: `[data-inspector-tab]` buttons toggle `[data-inspector-content]` visibility

### Environment controls
- `[data-env="force-disabled"]` — toggles disabled attribute on all inputs/buttons in preview
- `[data-env="force-loading"]` — adds `data-loading="true"` to preview root
- `[data-env="show-focus"]` — adds `.sc-show-focus` class (CSS rule: `* { outline: 2px solid blue !important }`)
- `[data-env="rtl"]` — sets `dir="rtl"` on preview container
- `[data-env="text-overflow"]` — replaces text content with long strings
- `[data-width]` buttons — set max-width on `.sc-preview-wrapper`

### Clipboard
- `#sc-copy-code` button — copies `#sc-code-output` text content to clipboard

### Resize handle
- Mousedown on `#sc-resize-handle` starts drag
- Mousemove updates `.sc-preview-wrapper` width
- Mouseup stops drag
