CONFIDENTIALITY: INTERNAL
STATUS: DRAFT - UNREVIEWED

# journlet/site

The landing page at [www.journlet.com](https://www.journlet.com). The bare apex `journlet.com`
redirects here. The app itself lives in [journlet/app](https://github.com/journlet/app) and is
served from `app.journlet.com`.

Two separate jobs: this repo holds the argument for using Journlet, that repo holds the thing
itself. Keeping them apart means the marketing page can change hourly without touching the app's
build, and the app's service worker never has to reason about a non-app route.

## What is here

```
index.html      the whole page
styles.css      tokens lifted from the app so both read as one product
CNAME           www.journlet.com
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

`www.journlet.com` is canonical; `journlet.com` redirects to it. GitHub Pages performs that
redirect itself, but only if the apex also points at Pages, so the apex A records below are
required even though nothing is served from the apex directly.

1. Settings → Pages → Source: **GitHub Actions**.
2. DNS at the registrar:
   - `CNAME` record, host `www`, value `journlet.github.io.`
   - four `A` records, host `@`, values `185.199.108.153`, `185.199.109.153`,
     `185.199.110.153` and `185.199.111.153`
   - leave the existing `app` CNAME and the email TXT records alone
3. Settings → Pages → Custom domain: enter `www.journlet.com`. The `CNAME` file in this repo
   keeps it set across deploys.
4. Tick **Enforce HTTPS** once GitHub has issued the certificate. This can take up to an hour
   after the DNS records propagate.

Each hostname is a separate custom domain as far as GitHub Pages is concerned, so this repo and
`journlet/app` can hold their own `CNAME` files without conflicting.

Everything user-facing points at `www`: the canonical link, the Open Graph URLs, the sitemap and
the robots directive. If the canonical host ever changes, those five places change with it.

## Local preview

```
python3 -m http.server 8000
```

Then open <http://localhost:8000>. Absolute paths (`/styles.css`) resolve correctly this way;
opening `index.html` directly from the filesystem will not load the stylesheet.
