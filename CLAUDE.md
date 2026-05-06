# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository purpose

This repo is the entire public web presence for CarLink (a car & spare-parts marketplace operating in Saudi Arabia and Egypt) — both the marketing homepage **and** the legal documents. Despite the `carlink-legal` repo name and the README framing it as a legal-docs site, `https://carlink.market` is verified to serve `index.html` from this repo (DNS A records point to GitHub Pages anycast `185.199.108-111.153`; `www` CNAMEs to `aahmedhammad87-lab.github.io`). Treat changes to `index.html` as production marketing-site edits, not just internal scaffolding.

There is no build step, no package manager, no tests, and no lint config. Committing to `main` publishes via GitHub Pages.

## Local preview

Open the file directly, or serve from the repo root:

```bash
python3 -m http.server 8000   # then visit http://localhost:8000/
```

## Architecture

Three top-level pages share conventions but **do not share assets** — each page inlines its own `<style>` and `<script>`. There is no shared CSS/JS file.

- `index.html` — landing page, links out to `privacy.html` and `terms.html`
- `privacy.html` — bilingual Privacy Policy
- `terms.html` — bilingual Terms of Service

### Bilingual pattern (privacy.html, terms.html)

Both legal pages embed English and Arabic content in the **same file**, gated by sibling `<div class="en">` and `<div class="ar">` blocks. Visibility is toggled by a `lang-en` / `lang-ar` class on `<body>`, applied by the inline `setLang()` script wired to the two `.lang-btn` buttons in the sticky `.lang-toggle` bar. The `.ar` block uses `direction: rtl` and reversed list margins.

When editing legal copy, **always update both `.en` and `.ar` blocks** so they stay in sync — the language toggle only swaps which block is visible; it does not translate.

### Duplicated shell across legal pages

`privacy.html` and `terms.html` are near-identical outside their `<main>` content: same `<style>` block, same header, same `.lang-toggle`, same footer, same `setLang()` script. Structural or styling changes (header markup, language-toggle behavior, brand color, typography) almost always need to be applied to **both files**. Treat them as mirrored templates.

Brand color `#C8102E` (CarLink red) is repeated inline in each page's `<style>`; change all occurrences together.

### Deployment

`CNAME` pins the GitHub Pages custom domain to `carlink.market` (verified live). Do not delete or rename it — GitHub Pages reads this file on every deploy to configure the domain, and removing it would break the production site.

## Operational Context

### What This Site Actually Is
This repo is the **public-facing front door for CarLink**:
- **carlink.market** = the main marketing homepage that potential users and dealers see first
- **carlink.market/privacy.html** = Privacy Policy (required by App Store)
- **carlink.market/terms.html** = Terms of Service (required by App Store)

The repo name "carlink-legal" is misleading — it's also the marketing landing page. Treat any change to index.html as a public-facing marketing change, not internal scaffolding.

### Deployment
- **Platform:** GitHub Pages (NOT Coolify, unlike most other CarLink repos)
- **Auto-deploy:** Push to `main` → GitHub Pages publishes within 1-2 minutes
- **Domain:** carlink.market (verified live via DNS A records and HTTP 200)
- **GitHub:** github.com/aahmedhammad87-lab/carlink-legal
- **No build step** — just edit HTML and push

### Why This Matters
- This is the **first impression** for new CarLink users
- Privacy + Terms are linked from the iOS App Store listing — breaking these pages can affect App Store compliance
- The bilingual (en/ar) toggle must work — Saudi users default to Arabic

### Workflow Rules (UPDATED - October 2026)

**CRITICAL: Local-first development. No automatic deployments.**

1. All work happens on `local-dev` branch by default. Never work directly on `main`.
2. All testing happens on `localhost`. Never test against production.
3. Commits are allowed on `local-dev` without asking. Push to `local-dev` is allowed without asking.
4. **NEVER push to `main` without explicit user instruction.** Phrases that authorize a push to main:
   - "push to main"
   - "deploy this"
   - "ship it"
   - "release to production"
   Do NOT interpret words like "fix", "do it now", "ok", or "go ahead" as authorization to push to main.
5. **NEVER trigger production deployment as part of any other task.** If user asks to "fix the brand filter", that means edit and test locally only — it does NOT mean push to main or deploy.
6. When work is complete on local-dev, ask the user explicitly: "Work is complete on local-dev. Should I push to main and deploy to production? [yes/no]"
7. After any push to main, always verify the deployment with a health check.
8. Never push to any GitHub org other than `aahmedhammad87-lab`.
9. Never suggest creating new repos.
10. When user says "fix" or "do it now" — proceed with the fix on local-dev. This is NOT authorization to deploy.

### Project Conventions
- Test changes locally first (`python3 -m http.server 8000`)
- Always verify both EN and AR versions render correctly after changes
- Privacy.html and terms.html share a duplicated shell — edit them in lockstep

### Related Repos
- `carlink-backend` — Node.js API at back.carlink.market
- `carlink-admin` — admin dashboard (Coolify, separate domain)
- `carlink-ios` — React Native mobile app on App Store (links to carlink.market/privacy.html and carlink.market/terms.html)

### Owner
- Mattar (also runs Jory Pack and Factory-X, based in Riyadh)
