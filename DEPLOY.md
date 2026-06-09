# Deploy & Ops — rangeway-pages (rangeway.co)

This is the **main Rangeway marketing site**. It is **self-hosted on a Hostinger VPS**
(not GitHub Pages), served by Nginx at **https://rangeway.co** (and `www`) from
`/var/www/rangeway-pages/` on the VPS `72.60.71.39`.

## How to deploy
**Push to `main`.** GitHub Actions (`.github/workflows/deploy.yml`) builds the Astro site and
rsyncs `dist/` to the VPS automatically (~1–2 min). No manual build or upload.
- Only `main` deploys; other branches don't.
- Watch it: repo → **Actions** tab, or `gh run watch`.
- If a deploy fails, the live site keeps serving its last good version — no outage. The deploy
  step retries rsync 3× with backoff (transient SSH/network blips self-heal); otherwise re-run
  the job from the Actions tab or push again.

## Local development
Astro (static). Node 22.
```bash
npm install
npm run dev        # http://localhost:4321
npm run build      # outputs to dist/
npm run preview    # serve the built dist/ locally
```

## TLS / DNS
- HTTPS via **Let's Encrypt** (certbot on the VPS) — auto-renews, nothing to manage.
- DNS at **Cloudflare, DNS-only (grey cloud)**: A records for `rangeway.co` + `www` → `72.60.71.39`.

## Infra notes
- Server path: `/var/www/rangeway-pages/`, Nginx server block for `rangeway.co www.rangeway.co`.
- CI auth: SSH deploy key in repo secrets `VPS_SSH_KEY` + pinned host key `VPS_KNOWN_HOSTS`.
- GitHub Pages is **disabled** for this repo — the VPS is the only host.
- A noindexed staging mirror of this site lives in **rangeway-preview** (preview.rangewayev.com)
  with an added Mojave/Climatize promo layer.
