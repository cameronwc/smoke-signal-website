# Smoke Signal Website

Landing page for [Smoke Signal](https://github.com/cameronwc/smoke-signal) — an iOS app for off-grid group communication.

## Setup

1. Push this repo to GitHub
2. Go to **Settings > Pages > Source** and select **GitHub Actions**
3. The site deploys automatically on every push to `main`

The deploy workflow validates HTML and runs Lighthouse CI checks before deploying.

## Local Development

Open `index.html` in a browser. No build step required.

## Environment Variables

Copy `.env.example` to `.env` for local config reference. The website is currently static HTML with no build-time env injection — `.env.example` documents the values you'll need when adding analytics, email signup, or App Store links.

## Structure

```
.
├── index.html                          # Landing page (single static file)
├── .env.example                        # Documented environment variables
├── .gitignore                          # Git ignore rules
├── .github/
│   ├── dependabot.yml                  # Automated dependency scanning
│   └── workflows/
│       └── deploy.yml                  # HTML validation + Lighthouse + Pages deploy
└── README.md
```
