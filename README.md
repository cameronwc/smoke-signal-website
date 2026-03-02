# Smoke Signal Website

Landing page for the [Smoke Signal](https://github.com/cameronking4/smoke-signal) iOS app. Hosted on GitHub Pages.

## Setup

1. Push this repo to GitHub
2. Go to **Settings > Pages > Source** and select **GitHub Actions**
3. The site deploys automatically on every push to `main`

## Local Development

Open `index.html` in a browser. No build step required — it's a single static HTML file.

## Structure

```
.
├── index.html                  # Landing page (all HTML/CSS/JS in one file)
├── .github/
│   └── workflows/
│       └── deploy.yml          # GitHub Actions workflow for Pages deployment
├── .gitignore
└── README.md
```
