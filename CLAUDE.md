# CLAUDE.md — Smoke Signal Website

Context file for Claude instances working on this repo.

---

## Project Overview

Marketing landing page for [Smoke Signal](https://github.com/cameronwc/smoke-signal), an iOS off-grid communication and navigation app. The site is a single static HTML file deployed to GitHub Pages.

**Live URL:** https://cameronwc.github.io/smoke-signal-website/

---

## Tech Stack

| Layer | Technology |
|-------|-----------|
| Markup | Single `index.html` — all HTML, CSS, and JS inline |
| Hosting | GitHub Pages via GitHub Actions |
| CI | `html-validate` + Lighthouse CI (accessibility ≥ 0.9, best-practices ≥ 0.9, SEO ≥ 0.9) |
| Build step | None — static file, no bundler or framework |

---

## File Structure

```
.
├── index.html                          # Entire website (HTML + embedded CSS + JS)
├── .env.example                        # Documented env vars for future features
├── .gitignore
├── .github/
│   ├── dependabot.yml                  # Automated dependency scanning
│   └── workflows/
│       └── deploy.yml                  # Validate → Lighthouse → Deploy pipeline
├── README.md
└── CLAUDE.md                           # This file
```

---

## Workflow

**All work must follow this process:**

1. **Branch from `main`**
   ```bash
   git checkout main && git pull origin main
   git checkout -b <type>/<short-description>
   ```
   Branch naming: `fix/`, `feat/`, `chore/`, `docs/`

2. **Make changes**
   - Edit `index.html` directly — CSS goes in the `<style>` block, JS at the bottom
   - No external CSS/JS files unless explicitly requested

3. **Validate locally before committing**
   ```bash
   npx html-validate index.html
   ```
   All errors must be resolved. Common issues:
   - Missing `type="button"` on non-submit `<button>` elements
   - Inline `style` attributes — extract to CSS classes instead
   - Heading hierarchy violations (no skipping levels)

4. **Commit and push**
   ```bash
   git add <specific-files>
   git commit -m "descriptive message"
   git push -u origin <branch-name>
   ```

5. **Create a Pull Request against `main`**
   ```bash
   gh pr create --title "Short title" --body "Description of changes"
   ```
   CI runs `html-validate` and Lighthouse on every PR. All checks must pass before merge.

---

## CI Pipeline (`.github/workflows/deploy.yml`)

| Job | Gate | What it checks |
|-----|------|---------------|
| `validate` | All pushes to main | `html-validate index.html` — structural HTML correctness |
| `lighthouse` | All pushes to main | Lighthouse CI with axe audits — accessibility, best-practices, SEO all ≥ 0.9 |
| `deploy` | After validate + lighthouse pass | Deploys to GitHub Pages |

**Performance scoring is disabled** (`categories:performance=off`) — static single-page site, not worth gating on.

---

## Coding Conventions

### HTML
- Semantic elements: `<header>`, `<main>`, `<section>`, `<footer>`
- All `<button>` elements need explicit `type` attribute
- All `<img>` / `<svg>` need `alt` text or `aria-hidden="true"` if decorative
- Heading order must not skip levels (`h1` → `h2` → `h3`, never `h1` → `h3`)

### CSS
- All styles live in the `<style>` block in `<head>`
- Design tokens defined as CSS custom properties on `:root`
- Dark theme by default (`--bg-primary: #0a0a0f`)
- Accent color: `--accent: #f97316` (orange)
- Responsive breakpoints: 768px (tablet), 480px (mobile)
- Use `.class` selectors over element selectors where possible
- No inline `style` attributes — html-validate will reject them

### JavaScript
- Minimal JS at bottom of `<body>` — mobile menu toggle, scroll animations, modal
- No frameworks or build tools
- IntersectionObserver for scroll-triggered animations

---

## Accessibility Requirements (Lighthouse Enforced)

These are checked in CI and will fail the build:

- **Color contrast**: All text must meet WCAG AA 4.5:1 ratio against its background. `--text-muted: #6b7280` is borderline on dark backgrounds — verify with WebAIM contrast checker before changing
- **Heading order**: Sequential, no skipped levels
- **Button/link labels**: `aria-label` must contain the visible text (WCAG 2.5.3 Label in Name)
- **Landmark regions**: Content must be within landmark elements (`<main>`, `<nav>`, etc.)
- **Decorative elements**: SVG icons and emoji used as decoration need `aria-hidden="true"`

---

## Content Rules

- **No mention of Meshtastic** — legal constraint. Refer to "LoRa radio", "mesh radio", or "compatible LoRa device" instead
- The app works with any BLE-compatible LoRa radio (SenseCAP T1000-E, Heltec, RAK, etc.)
- Feature descriptions should match current app capabilities — reference the [smoke-signal repo](https://github.com/cameronwc/smoke-signal) README for source of truth
- Privacy-forward messaging: no accounts, no cloud, data stays on device

---

## Common Tasks

### Adding a new feature card
Feature cards are in the `.features-grid` section. Each card follows this template:
```html
<div class="feature-card">
    <div class="feature-icon">ICON</div>
    <h3>Title</h3>
    <p>Description</p>
</div>
```

### Updating the "How It Works" section
Three-step flow in `.steps-grid`. Keep it to exactly 3 steps.

### Running Lighthouse locally
```bash
npm install -g @lhci/cli
lhci autorun --collect.staticDistDir=. \
  --assert.preset=lighthouse:recommended \
  --assert.assertions.categories:performance=off \
  --assert.assertions.categories:accessibility=["error", {"minScore": 0.9}] \
  --assert.assertions.categories:best-practices=["error", {"minScore": 0.9}] \
  --assert.assertions.categories:seo=["error", {"minScore": 0.9}]
```

---

## Known Issues / Backlog

Track issues in [Linear](https://linear.app) under the Smoke Signal team with website-related labels. Key blockers:
- Privacy policy page needed for App Store submission
- App Store / TestFlight links are placeholder until app is published
