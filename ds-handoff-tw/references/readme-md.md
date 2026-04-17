# README.md — Structure & Rules

README is the first thing a human reads on GitHub.
Audience: developers receiving the handoff, designers checking the repo.
Tone: clear and direct. No buzzwords.

## Required sections

### 1. Header
Project name + one-line description. Optional: badge for last Figma export date.

### 2. What's in this repo
- Components list (linked to showcase if available)
- Token reference
- Showcase URL
- Patterns (if `/patterns/` folder exists): list pattern files with links to their `.html` previews
  ```md
  ### Patterns
  | Pattern | Spec | Preview |
  |---------|------|---------|
  | RegistrationForm | [spec](patterns/RegistrationForm.spec.md) | [preview](patterns/RegistrationForm.html) |
  ```
  Only include this section if `/patterns/` exists and contains at least one pattern.

### 3. Quick start
```bash
# No build step required — open index.html directly
# Or serve locally:
npx serve .
```

### 4. Using components
Show ONE real import example with actual props. Use a component from the project.
```tsx
import { Button } from './src/components/Button'

<Button variant="Primary" size="M" startIcon={<PlusIcon />}>
  Save
</Button>
```

### 5. Token system
Explain the pattern in 2-3 sentences. Show one example of token → CSS var → usage.
Link to tokens.css.

### 6. Figma source
- File link
- Last export date
- How to re-export (one-line: "Export tokens from Variables panel → replace tokens.css")

### 7. Project structure
Same tree as CLAUDE.md but friendlier — add comments explaining each folder.

### 8. Status (optional if STATUS.md exists)
Brief table or link to STATUS.md.

## What to omit
- License section (unless client explicitly wants it)
- Badges for CI/coverage/npm (not relevant for handoff package)
- Contribution guidelines (that's CONTRIBUTING.md)
- Long philosophy sections

## Tone rules
- Write for someone who's never seen the repo
- Assume TypeScript knowledge, not design system theory
- Short paragraphs — max 3 sentences each
