# Figma to Code Skills

Ten Claude Code skills that turn a Figma design system into production-ready React + Tailwind code. Designer-led, AI-powered.

> **Status: MVP.** Shipped to prove the workflow end-to-end on real projects. Names, outputs, and references may change. Expect sharp edges.

## What this is

A workflow, not a plugin. Each skill is a standalone instruction set that Claude Code follows to handle one part of the design-to-code pipeline: setup, component extraction, QA, preview, docs, handoff. Together they replace days of manual frontend work with an afternoon of orchestration.

- **Stack:** React + TypeScript + Tailwind CSS
- **Runtime:** [Claude Code](https://claude.com/claude-code)
- **Input:** a Figma file with variables and components
- **Output:** a repo with typed components, tokens, showcase, and docs

## Install

Clone the repo and copy the skill folders into your Claude Code skills directory:

```bash
git clone https://github.com/Properly-DEV/figma-to-code-skills.git
cp -r figma-to-code-skills/ds-*-tw ~/.claude/skills/
```

Restart Claude Code. The skills will auto-trigger based on natural-language prompts (see each skill's `SKILL.md` for trigger phrases), or you can invoke them by name.

## Skills

| Skill | Purpose |
|---|---|
| `ds-session-start-tw` | Read project state at the start of every session |
| `ds-setup-tw` | Bootstrap a new design system from Figma exports, or refresh tokens |
| `ds-figma-to-react-tw` | Convert a single Figma component into production React code |
| `ds-figma-templates-tw` | Extract a full page layout (grid, sections, responsive rules) |
| `ds-figma-patterns-tw` | Extract a multi-component view (chat, form, dashboard section) |
| `ds-qa-tw` | Audit and refactor components to production quality |
| `ds-preview-tw` | Generate a standalone HTML preview for a single component |
| `ds-showcase-tw` | Build a three-panel Storybook-like interactive showcase |
| `ds-status-changelog-tw` | Maintain STATUS.md and CHANGELOG.md for the system |
| `ds-handoff-tw` | Generate GitHub handoff docs (CLAUDE.md, README.md, CONTRIBUTING.md) |

## Workflow

See [WORKFLOW.md](./WORKFLOW.md) for the three-phase pipeline (Prepare → Build → Ship) and the order in which these skills are designed to run.

## Feedback

This is an early public release. If you try it, break it, or ship with it, [open an issue](https://github.com/Properly-DEV/figma-to-code-skills/issues) or reach out. Especially interested in: what broke on your Figma file, what output surprised you, what's missing.

## License

MIT
