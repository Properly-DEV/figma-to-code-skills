# Workflow

Three phases. Run the skills in this order the first time. After that, most work lives in the Build phase.

## 01 · Prepare

Orient Claude in your project, then establish the design system foundation.

- **`/ds-session-start-tw`** — Run at the start of every session. Claude reads your project state (tokens, components built so far, status) and knows where to pick up.
- **`/ds-setup-tw`** — One-time or when Figma changes. Reads your Figma variables and SVG exports, writes `tailwind.config.js`, tokens, and the repo skeleton. Result: a foundation ready for components.

## 02 · Build

The heavy lifting. Each skill takes a Figma URL and produces code. Can run in parallel (one agent per component).

- **`/ds-figma-to-react-tw`** — Single component. Typed props, variants, forwardRef, accessibility, Tailwind classes bound to tokens.
- **`/ds-figma-templates-tw`** — Full page layout. Grid, spacing, sections, responsive behavior as a structural wireframe.
- **`/ds-figma-patterns-tw`** — Multi-component view (chat, form, dashboard). Composition of components with behavior spec.
- **`/ds-qa-tw`** — Audit and refactor an existing component. Catches token violations, accessibility gaps, and broken conventions.
- **`/ds-preview-tw`** — Standalone HTML preview of a single component. Open in browser, compare side-by-side with Figma.

## 03 · Ship

Make the system discoverable and handoff-ready.

- **`/ds-showcase-tw`** — Interactive Storybook-like showcase. Pure HTML/CSS/JS, no build step. Deploy to GitHub Pages or open locally.
- **`/ds-status-changelog-tw`** — Maintain `STATUS.md` and `CHANGELOG.md`. What's done, what's in progress, what changed.
- **`/ds-handoff-tw`** — Generate `CLAUDE.md`, `README.md`, and `CONTRIBUTING.md` so the repo is self-explanatory to any dev, client, or future AI agent.

## Recommended first run

1. Export Figma variables and SVGs into a folder.
2. Open Claude Code in the project root.
3. Run `/ds-setup-tw`. Answer the prompts.
4. Pick one Figma component URL. Run `/ds-figma-to-react-tw`.
5. Run `/ds-preview-tw` on the same component. Open the preview in a browser, compare with Figma.
6. Iterate with `/ds-qa-tw` if the output needs cleanup.
7. When you have 3–5 components, run `/ds-showcase-tw` to see them all.
8. Before sharing the repo, run `/ds-handoff-tw` to generate the docs.

## Tips

- **Pixel accuracy** comes from well-structured Figma files. Variables bound to components, auto-layout used consistently, variants named sensibly. The skills read what you designed.
- **Parallel runs** work best in [Conductor](https://conductor.build) or by opening multiple Claude Code instances on the same repo.
- **Review every output.** These skills produce senior-dev first drafts, not final code. Treat it like a pull request.
