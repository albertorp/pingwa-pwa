# CLAUDE.md — PingWA PWA

This file provides guidance for AI assistants (Claude and others) working in this repository.

## Project Overview

**PingWA** is a minimal Progressive Web App (PWA) that lets users start a WhatsApp conversation with any phone number without saving it to contacts first. It generates a `wa.me` deep link and opens WhatsApp in a new tab.

- **Live app:** `https://albertorp.github.io/pingwa-pwa/`
- **Deployed via:** GitHub Pages (auto-serves `index.html` from `main`)
- **No backend, no build step, no dependencies**

---

## Architecture

This is an intentionally ultra-minimalist single-file architecture. **All application code lives in `index.html`.** There is no framework, no bundler, no package manager, and no compilation step.

```
pingwa-pwa/
├── index.html                    # Entire app: markup, styles (Tailwind), JS logic
├── manifest.json                 # PWA metadata (name, icon, theme color, start_url)
├── sw.js                         # Minimal service worker (enables PWA install)
├── pingwa_logo.png               # Logo shown in the UI (h-32 w-32 in app)
├── Icon-App-1024x1024@1x.png    # Home screen icon (1024x1024, used in manifest)
└── README.md                     # End-user documentation
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Markup | HTML5 |
| Styling | Tailwind CSS v3 (loaded via CDN — no local install) |
| Logic | Vanilla JavaScript (ES6+, no transpilation) |
| Analytics | PostHog (EU region, pageviews/sessions only) |
| Hosting | GitHub Pages |
| PWA | Web App Manifest + Service Worker |

**No npm, no package.json, no node_modules, no build artifacts.** If you're looking for those, they don't exist by design.

---

## Development Workflow

### Running locally

Open `index.html` directly in a browser (file:// is fine) or serve it with any static server:

```bash
# Option 1: Python (no install needed)
python3 -m http.server 8080

# Option 2: Node (if available)
npx serve .
```

Then open `http://localhost:8080` in a browser.

### Making changes

1. Edit `index.html` (or `manifest.json` / `sw.js` for PWA config)
2. Refresh the browser
3. Commit and push to `main` — GitHub Pages redeploys automatically

**There is no build step, no linting script, no test suite, and no CI pipeline.**

### Deployment

Pushing to `main` triggers GitHub Pages auto-deployment. No additional steps needed.

---

## index.html Structure

The file is divided into these logical sections:

| Section | Lines | Description |
|---|---|---|
| HTML head / meta | 1–12 | charset, viewport, manifest link, PWA meta tags |
| PostHog analytics | 13–21 | Analytics init script (obfuscated loader + config) |
| Tailwind config | 22–36 | Inline config extending default theme with brand colors |
| UI markup | 38–90 | Logo, card, input, button, error message |
| Input keydown handler | 92–95 | Triggers `go()` on Enter key |
| `go()` function | 97–132 | Core logic: validation + WhatsApp link generation |
| Service worker reg | 134–138 | Registers `sw.js` for PWA support |

---

## Core Logic: `go()` Function

The `go()` function (defined in `index.html`) handles all user interaction:

1. **Read** the raw input from `#phone`
2. **Reject** any characters that aren't digits, `+`, or spaces — show error
3. **Strip** whitespace and **parse** with regex `/^(\+?)(\d+)$/`
4. **Reject** if parsing fails or digit count is less than 7
5. **Construct** `fullNumber = optionalPlus + digits`
6. **Open** `https://wa.me/{fullNumber}` in a new tab via `window.open(..., "_blank")`

Error display uses `classList.remove("hidden")` on `#error` and sets `#error-text` content.

---

## Styling Conventions

- **Tailwind utility classes only** — no custom CSS, no `<style>` blocks
- **Custom brand colors** (defined in inline `tailwind.config`):
  - `brand-dark`: `#0d4a4f` (primary teal — buttons, headings)
  - `brand-mint`: `#7fdfd0` (focus ring, accents)
  - `brand-light`: `#e6f7f5` (background gradient start)
- Use existing Tailwind patterns from the file when adding UI: `rounded-xl`, `shadow-md`, `text-sm`, `border-2`, etc.
- The layout is centered with `max-w-md` on a gradient background

---

## PWA Configuration

### manifest.json

Key fields to keep consistent if updating:
- `start_url`: `/pingwa-pwa/` (must match GitHub Pages subdirectory)
- `display`: `standalone` (hides browser chrome when installed)
- `theme_color` / `background_color`: must match `brand-dark` / `brand-light`

### sw.js

Minimal two-line service worker. It immediately activates and claims all clients, which is the minimum needed for PWA installability. Do not add caching strategies here unless there's a specific need — the app works fine online-only.

---

## Analytics

PostHog is initialized in `index.html` with:
- **Key:** `phc_YCAIKhPRh5IhbSLPyx6jrt2bQbInY9kzKgLbg7Vjmkx`
- **Host:** `https://eu.i.posthog.com` (EU region for GDPR)
- **Person profiles:** `identified_only` (no anonymous profiles created)
- **Defaults date:** `2026-01-30`

Only automatic pageview/session events fire — no custom `posthog.capture()` calls. This key is intentionally public (analytics-only, no sensitive permissions).

---

## Git Conventions

- **Main branch:** `main` — production, auto-deployed to GitHub Pages
- **Feature branches:** follow `claude/<description>` or descriptive naming
- **Commit messages:** imperative mood, concise, no emoji
  - Good: `Add PWA support: manifest, service worker, and iOS meta tags`
  - Good: `Fix PostHog snippet: update to latest version and switch to EU region`
- Always push feature work to a branch and merge to `main` via PR

---

## What NOT to Do

- **Do not add a build step** (Webpack, Vite, etc.) unless explicitly requested — the zero-build architecture is intentional
- **Do not add a package.json / npm dependencies** unless explicitly requested
- **Do not split `index.html`** into separate JS/CSS files unless explicitly requested
- **Do not add a framework** (React, Vue, etc.) unless explicitly requested
- **Do not add caching to `sw.js`** without careful consideration — it can cause stale content issues
- **Do not change `start_url`** in `manifest.json` without updating the GitHub Pages base path
- **Do not add TypeScript** — the app is intentionally plain JavaScript

---

## Common Tasks

### Add a new UI element
Edit `index.html` directly. Use Tailwind utility classes consistent with the existing pattern. Brand colors are `brand-dark`, `brand-mint`, `brand-light`.

### Change validation logic
The entire validation flow is in the `go()` function in `index.html` (lines ~97–132). Modify the regex patterns or conditions there.

### Update app metadata (name, icon, colors)
- **Browser title / display name:** `<title>` tag and `<meta name="apple-mobile-web-app-title">` in `index.html`
- **PWA metadata:** `manifest.json`
- **Brand colors:** `tailwind.config` inline script in `index.html`
- **Icons:** Replace `pingwa_logo.png` and/or `Icon-App-1024x1024@1x.png`, update references in `manifest.json` and `index.html`

### Disable or replace analytics
Remove or replace the PostHog `<script>` block in `index.html` (lines ~14–21).
