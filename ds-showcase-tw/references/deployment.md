# Deployment Guide

The showcase is pure static HTML/CSS/JS with no build step. It can be deployed anywhere that serves static files.

## Local Usage

Just open `showcase/index.html` in any browser. No server required.

All paths within the showcase are relative, so it works directly from the filesystem. The tokens CSS file is referenced via a relative path from each page (e.g., `../../tokens/tokens.css` from component pages).

## GitHub Pages (Primary)

### Setup steps

1. Push the repository to GitHub (if not already)
2. Go to the repository on GitHub
3. Click **Settings** tab
4. In the left sidebar, click **Pages**
5. Under "Build and deployment":
   - Source: **Deploy from a branch**
   - Branch: **main**
   - Folder: **/ (root)**
6. Click **Save**
7. Wait 1-2 minutes for the first deployment

The showcase will be available at: `https://{username}.github.io/{repo-name}/showcase/`

### Path considerations

GitHub Pages serves from the repo root, so the showcase URL includes the `/showcase/` prefix. All internal links use relative paths, so they work correctly.

Token CSS path from component pages: `../../tokens/tokens.css`
Token CSS path from foundation pages: `../../tokens/tokens.css`
Token CSS path from index.html: `../tokens/tokens.css`

### Auto-redeploy

Every push to `main` triggers a redeploy automatically. No CI/CD configuration needed.

### Custom domain (optional)

After the first deployment:
1. Go to Settings > Pages
2. Under "Custom domain", enter your domain
3. Add a CNAME record in your DNS pointing to `{username}.github.io`
4. Optionally check "Enforce HTTPS"

## Vercel (Alternative)

Vercel deploys static files with zero configuration.

### Setup steps

1. Push repo to GitHub (if not already)
2. Go to vercel.com > New Project > Import Git Repository
3. Framework Preset: **Other** (not Next.js, not Vite)
4. Root Directory: `.` (repo root)
5. Build Command: leave **empty**
6. Output Directory: `.` (repo root)
7. Click Deploy

The showcase will be at: `https://{project-name}.vercel.app/showcase/`

### Token path fix for Vercel

If tokens.css is referenced with `../../tokens/tokens.css` from component pages, this works correctly because Vercel serves the full repo structure.

### vercel.json (only if needed)

For custom routing or headers:
```json
{
  "headers": [
    {
      "source": "/(.*)",
      "headers": [{ "key": "Cache-Control", "value": "public, max-age=300" }]
    }
  ]
}
```

Usually not needed for a static showcase.

### Auto-redeploy

Every push to `main` triggers a redeploy automatically.

## Netlify (Alternative)

1. Push repo to GitHub
2. Go to app.netlify.com > Add new site > Import from Git
3. Branch: **main**
4. Build command: leave empty
5. Publish directory: `.`
6. Click Deploy

## Path Reference Table

| Page location | tokens.css path | showcase.css path | showcase.js path |
|---|---|---|---|
| `showcase/index.html` | `../tokens/tokens.css` | `css/showcase.css` | `js/showcase.js` |
| `showcase/components/*.html` | `../../tokens/tokens.css` | `../css/showcase.css` | `../js/showcase.js` |
| `showcase/foundations/*.html` | `../../tokens/tokens.css` | `../css/showcase.css` | `../js/showcase.js` |
| `showcase/patterns/*.html` | `../../tokens/tokens.css` | `../css/showcase.css` | `../js/showcase.js` |
