# Project notes for Claude Code

Owner: Abd Elrahman Sheta (marketing + AI automation, Dubai). Talks in Egyptian Arabic; keep replies short and practical, and avoid mixing English words inside Arabic sentences (put English names on their own line or in code blocks).

## What lives here
- `portfolio/` — public portfolio (index, results, toolkit, agent-hq, system-map, eat-track, menu). Public on Cloudflare Pages project `abdelrahman-sheta` → https://abdelrahman-sheta.pages.dev/portfolio/
- `index.html`, `sales.html`, `data.json` — restaurant intelligence demo (data.json is rewritten daily by n8n).
- `glowup/` — Glow Up Clinic demo dashboard, **built output only**:
  - `glowup/index.html` is an AES-256-GCM encrypted wrapper (PBKDF2-SHA256, 310k iterations). Never edit it by hand.
  - `glowup/status.json` is rewritten daily by n8n (commit message "glowup status YYYY-MM-DD").
  - `glowup/_headers` — security headers for Cloudflare.
- The plaintext dashboard source and `build.py` are **not** in this repo on purpose (the repo is public). They live in a private folder on the owner's Mac: `glowup-src/`.

## Editing the clinic dashboard
1. Edit `../glowup-src/clinic-dashboard-source.html` (plaintext; older copies call it `index.html`).
2. Run `python3 ../glowup-src/build.py` (needs `pip install cryptography`); it writes `glowup/index.html` here. Login users and the visit-alert webhook are set inside build.py.
3. `git pull --rebase` first (n8n pushes daily commits), then commit and push. Cloudflare redeploys in about a minute.
Never commit the plaintext source, passwords, tokens, or the Telegram chat id.

## Hosting
- GitHub Pages (this repo) still serves everything publicly — kept on for the clinic management demo link.
- Cloudflare Pages projects (account sheeta.ae@gmail.com):
  - `glowup-dashboard` → https://glowup-dashboard.pages.dev (output dir `glowup`), locked by Cloudflare Access policy "Glow Up allowed emails".
  - `abdelrahman-sheta` → portfolio (build copies portfolio + restaurant files into `public/`).
  - `glowup-workflow-and-portfolio` → stray duplicate that bypasses the lock; pending deletion.

## n8n (abdelrahmansheta.app.n8n.cloud)
- "Glow Up · Daily Growth Brief (demo)" — 08:00 Dubai daily: demo data → KPIs in code → OpenRouter writes the brief → Telegram → updates `glowup/status.json` via GitHub API.
- "Daily Restaurant Report – Sales + Competitors (Demo)" — updates `data.json`.
- "Glow Up · Dashboard visit alerts" — webhook `/webhook/glowup-visit`, only accepts the two dashboard origins, emails + Telegrams the owner on every visit.
- Do not call the n8n REST API from a logged-in browser page (it logged the owner out once).

## Rules
- Numbers are computed in code; the AI only writes the text.
- No patient names or phone numbers anywhere; aggregates only.
- Viewers must never be able to edit anything.
- Before any `git push`, show the owner a short summary of what will change and wait for their OK.
