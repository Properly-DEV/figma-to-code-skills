# Template Spec Format

Use this exact structure when generating `templates/{TemplateName}.spec.md`.

Replace all placeholder values (`{TemplateName}`, `{figma_url}`, `{date}`) with actual values.

---

## Spec structure

The output file must contain these sections in order:

### 1. Header

```
# {TemplateName}

> Full page layout blueprint. Structural grid only — no component logic or real content.
> Figma source: {figma_url}
> Generated: {date}
```

### 2. Page Anatomy

Hierarchical list of all sections with inline layout rules. Example:

```
## Page Anatomy

- **Header** (fixed top, h-16, full-width)
  - Logo
  - Navigation
  - Search + Actions
- **Main** (flex row, gap-lg, flex-1)
  - **Sidebar** (w-64, fixed height, overflow-y-auto)
    - Nav links
    - Settings
  - **Content** (flex-1, min-w-0)
    - Chart area (flex-1, min-h-[400px])
    - Data table (flex-1)
- **Footer** (h-12, full-width)
  - Status bar
```

### 3. Grid & Layout Rules

Table with one row per section:

| Section | Width | Height | Position | Overflow | Direction |
|---------|-------|--------|----------|----------|-----------|
| Header | 100% | 64px | fixed top | visible | row |
| Sidebar | 256px | calc(100vh - 64px) | sticky | scroll-y | column |
| Content | flex-1 | auto | static | visible | column |
| Footer | 100% | 48px | fixed bottom | visible | row |

### 4. Spacing

Table of gaps between sections with token references:

| Location | Value | Token | Tailwind |
|----------|-------|-------|----------|
| Header ↔ Main | 0 (adjacent) | — | — |
| Sidebar ↔ Content | 16px | --spacing-xl | gap-xl |
| Chart ↔ Data table | 16px | --spacing-xl | gap-xl |
| Content inner padding | 24px | --spacing-2xl | p-2xl |

### 5. Responsive Breakpoints

| Breakpoint | Change |
|------------|--------|
| ≤1280px | Content padding reduces to 16px |
| ≤1024px | Sidebar collapses to icon-only (w-16) |
| ≤768px | Sidebar hidden, hamburger menu in Header; all content stacks vertically |
| ≤480px | Footer hidden; bottom nav replaces it |

### 6. Section → Pattern/Component Map

| Section | Pattern/Component | Status |
|---------|------------------|--------|
| Header | AppHeader | ✅ exists |
| Sidebar | SideNav | ✅ exists |
| Chart area | (chart library) | 🔲 slot (external) |
| Data table | DataTable pattern | 🔲 not built |
| Footer | StatusBar | ✅ exists |

Status legend:
- ✅ exists — component/pattern is built and in `/components/` or `/patterns/`
- 🔲 not built — needs to be created
- 🔲 slot (external) — filled by external library or user code, not part of DS

### 7. AI Agent Instructions

Complete JSX layout shell as a named export. This must be a compilable React component, not pseudocode. Example:

```tsx
export default function {TemplateName}Page() {
  return (
    <div className="min-h-screen flex flex-col">
      {/* Header — fixed top */}
      <header className="fixed top-0 left-0 right-0 h-16 z-50 bg-primary border-b border-secondary">
        {/* AppHeader */}
      </header>

      {/* Main — below header */}
      <div className="flex flex-1 pt-16 pb-12">
        {/* Sidebar — sticky */}
        <aside className="w-64 sticky top-16 h-[calc(100vh-64px)] overflow-y-auto border-r border-secondary
                          max-lg:w-16 max-md:hidden">
          {/* SideNav */}
        </aside>

        {/* Content */}
        <main className="flex-1 min-w-0 flex flex-col gap-xl p-2xl max-lg:p-xl">
          {/* Chart area */}
          <section className="flex-1 min-h-[400px]">
            {/* Chart slot */}
          </section>

          {/* Data table */}
          <section className="flex-1">
            {/* DataTable */}
          </section>
        </main>
      </div>

      {/* Footer — fixed bottom */}
      <footer className="fixed bottom-0 left-0 right-0 h-12 z-50 bg-primary border-t border-secondary
                         max-sm:hidden">
        {/* StatusBar */}
      </footer>
    </div>
  );
}
```

---

## Rules for the agent

- Tailwind classes must use project tokens from `tailwind.config.ts` — never raw hex/px in className.
- The AI Agent Instructions JSX must be a complete, compilable component — not pseudocode.
- Responsive classes use Tailwind's `max-*` prefix (mobile-first is default, but `max-*` reads better for "at this breakpoint, change").
- Keep section comments short — just the pattern/component name that goes there.
- Value resolution: match observed px values to existing Tailwind classes via `tailwind.config.ts` first. If no match exists, propose adding a token. Never use arbitrary values (`gap-[16px]`) when a mapped class exists.
