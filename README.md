CONFIDENTIALITY: INTERNAL
STATUS: DRAFT - UNREVIEWED

# journlet/site

The landing page at [journlet.com](https://journlet.com). The app itself lives in
[journlet/app](https://github.com/journlet/app) and is served from `app.journlet.com`.

Two separate jobs: this repo holds the argument for using Journlet, that repo holds the thing
itself. Keeping them apart means the marketing page can change hourly without touching the app's
build, and the app's service worker never has to reason about a non-app route.

## What is here

```
index.html      the whole page
styles.css      tokens lifted from the app so both read as one product
CNAME           journlet.com
robots.txt      + sitemap.xml, for the search-listing route to discovery
favicon.svg     copied from app/public
apple-touch-icon.png
og-image.png    link preview card
```

No build step, no dependencies, no JavaScript at all. A push to `main` publishes the repo root
to GitHub Pages via `.github/workflows/deploy.yaml`.

## Design notes

- Tokens (`--ink`, `--paper`, `--line`, the 33px dot pitch, Fraunces and Public Sans) are copied
  from `app/src/index.css` and `app/src/lib/grid.ts`. If those change in the app, change them
  here too, or the two surfaces start to drift apart.
- The sample spread in the hero is real markup, not a screenshot. It cannot go stale, it scales
  cleanly, and search engines read the notation as text.
- The notation grid is both the product argument and the main SEO surface. The eighth cell
  ("no emoji, no checkboxes, no substitutes") is the only competitive positioning on the page.
- No third-party scripts, and a CSP with `script-src 'none'`, matching the app's posture in
  spec §6.2. There is no analytics on this page by design; if that changes, it should be a
  deliberate decision rather than a default.

## One-time setup

1. Settings → Pages → Source: **GitHub Actions**.
2. DNS at the registrar: four A records for the apex `journlet.com` pointing at
   `185.199.108.153`, `185.199.109.153`, `185.199.110.153` and `185.199.111.153`. Leave the
   existing `app` CNAME record pointing at `journlet.github.io` alone.
3. Settings → Pages → Custom domain: enter `journlet.com`. The `CNAME` file in this repo keeps
   it set across deploys.
4. Tick **Enforce HTTPS** once GitHub has issued the certificate. This can take up to an hour
   after the DNS records propagate.

The apex and the subdomain are separate custom domains as far as GitHub Pages is concerned, so
both repos can hold their own `CNAME` without conflicting.

## Local preview

```
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Absolute paths (`/styles.css`) resolve correctly this way;
opening `index.html` directly from the filesystem will not load the stylesheet.
