# React Showcase — When and How

Use this only if the user explicitly asks for a React-based showcase
(e.g., wants hot reload during development, interactive controls, or search).

For most handoffs, the static `index.html` is better — no build step,
no dependencies, nothing to break.

## When React makes sense

- Client will continue developing the design system actively
- Need interactive prop controls (like Storybook's controls panel)
- Need search across components
- Team is comfortable with npm/Node

## Minimal setup (Vite)

```bash
npm create vite@latest showcase -- --template react-ts
cd showcase
npm install
```

Copy `tokens.css` into `showcase/src/tokens.css` and import in `main.tsx`:
```tsx
import './tokens/tokens.css'
```

Copy component files into `showcase/src/components/`.

## Page structure

Same logical structure as `index.html` — one section per component,
variant groups, cells with labels. Just JSX instead of HTML.

## Deploying Vite to Vercel

```json
// vercel.json
{
  "buildCommand": "npm run build",
  "outputDirectory": "dist",
  "framework": "vite"
}
```

Or: Vercel auto-detects Vite if `package.json` has `"vite"` in devDependencies.

## Interactive controls (optional)

For prop controls without Storybook, a minimal approach:
```tsx
// Simple state-based prop switcher
const [variant, setVariant] = useState<ButtonVariant>('Primary')
const [size, setSize] = useState<ButtonSize>('M')

return (
  <>
    <select onChange={e => setVariant(e.target.value as ButtonVariant)}>
      {['Primary','Secondary','Tertiary','Quaternary'].map(v =>
        <option key={v}>{v}</option>
      )}
    </select>
    <Button variant={variant} size={size}>Preview</Button>
  </>
)
```

This is optional — static variant grids are more readable for handoff.
