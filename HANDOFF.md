# HANDOFF — sabahwebs.com (Webflow clone + SEO, hosted on GitHub Pages)

**Last updated:** 2026-06-09
**Subject site:** sabahwebs.com — web design + SEO agency, Sabah Malaysia (+ Australia secondary)
**Owners:** Bing & Liang (two partners) · WhatsApp/phone **+60 16-843 0891** · happycodesmy@gmail.com
**Repo:** https://github.com/fyb27/sabahwebs.com (public, branch `main`)
**Live (GitHub clone):** https://fyb27.github.io/sabahwebs.com/  ← serves directly, no redirect
**Real site (unchanged):** https://sabahwebs.com is still on **Webflow** (Cloudflare DNS). Never touched.
**Local working copy:** `Z:\sites\sabahwebs happycodes\sabahwebs`

---

## 0. Mental model (important)

There are **two separate, independent sites**:

| | Real site | GitHub clone |
|---|---|---|
| URL | `sabahwebs.com` | `fyb27.github.io/sabahwebs.com/` |
| Host | Webflow / Cloudflare | GitHub Pages |
| Status | live, untouched | live, this repo |

The clone does **not** point at or affect `sabahwebs.com` (Pages custom domain is removed,
`cname` is null). They only connect if the owners later repoint DNS to GitHub (their call).

---

## 1. What was done

Cloned sabahwebs.com **off Webflow** into a self-contained static site, applied the
2026-06-08 SEO audit fixes, then iterated on design/content per owner feedback.

- **Cloned** 11 original pages (home + 10 blog posts) + all assets locally. Zero Webflow CDN
  dependency. Original extensionless URLs preserved.
- **New pages built:** `/services` (hub), `/web-design-sabah`, `/seo-services-sabah`,
  `/web-design-kota-kinabalu`, `/ecommerce-website-sabah`, `/webflow-website-sabah`,
  `/about`, `/contact`, `/blog` (index, fixes the old 404), `404.html`.
- **Deployed** on GitHub Pages, served at the github.io project URL with **relative asset
  paths** (so it works both at that subpath and, later, at the apex domain).

---

## 2. Serving model (read before touching paths)

- The site uses **relative** asset/link paths (`assets/...`, `../assets/...`). This is what
  lets it render at `fyb27.github.io/sabahwebs.com/`. **Do not** revert to root-absolute
  `/assets` paths or the subpath build breaks.
- `relativize.mjs` (pipeline step 7) does this conversion. Every new HTML file MUST be listed
  in its `rootPages`/blog arrays or it ships unstyled (this bit us once with `services.html`).
- Canonical / og / hreflang / JSON-LD URLs stay **absolute** (`https://sabahwebs.com/...`) on
  purpose, so the apex remains the SEO canonical and the github.io copy isn't indexed.
- The custom domain was **removed** from Pages so github.io serves directly (no redirect).
  Bare `…/sabahwebs.com` → 301 → `…/sabahwebs.com/` is just GitHub adding a trailing slash
  (normal for any project page), not a jump to Webflow.

### To move the clone onto sabahwebs.com later
1. Repo → Settings → Pages → Custom domain → `sabahwebs.com` (recreates CNAME).
2. DNS: `A @ → 185.199.108–111.153`; `CNAME www → fyb27.github.io`; remove Webflow records.
3. Enable Enforce HTTPS once the cert provisions. Relative paths already work at the apex.

---

## 3. Theme + design notes

- The site is a **LIGHT theme**: page background `#fafafa`, body text `#393e46`. NB the
  Webflow class `background_dark` is misleadingly named — it resolves to the off-white
  `--base-color-brand--background`. Style new components dark-on-light, not light-on-dark.
- Brand: primary `#f25836` (orange), teal `#00adb5`, ink `#222831`, cream `#f3dfa2`.
- All custom styling for the new pages + enriched footer lives in `assets/site/custom.css`
  (the `.sw-*` classes). Webflow's own CSS still powers the homepage/blog + navbar/footer shell.
- **No em dashes** anywhere on the site (owner preference) — enforced by `cleanup.mjs`.
- **No prices** on service pages (every project differs) — pricing sections are a no-number
  "How we price / get a quote" block; the schema `offers` are stripped. The two blog articles
  that discuss Malaysian SEO/website pricing keep their figures (editorial, intentional).
- Discoverability: service pages are reachable from the homepage via (a) the body
  "Our services" cards, (b) top-nav "Services" → `/services` hub, (c) footer links.
- Web Design Sabah + Kota Kinabalu are presented as **one** card/footer entry (de-duped); the
  KK page still exists (good for local SEO) and is linked from the hub's web-design card.

---

## 4. Build pipeline (re-runnable; scripts in `_build/`, gitignored)

Run from repo root, in this order. A fresh `mirror` is required first — `seo-fix` is NOT
idempotent (re-running twice double-injects).

```
node _build/mirror.mjs           # download 11 pages + assets from live Webflow
node _build/seo-fix.mjs          # sitewide SEO + migration fixes to the 11 pages
                                 #   (schema, hreflang, canonical, og, fonts, footer,
                                 #    nav Services->/services, bylines, home "Our services")
node _build/extract-chrome.mjs   # snapshot navbar/footer/scripts -> _build/chrome.json
node _build/build-pages.mjs      # build the 9 new pages (no prices)
node _build/build-services-hub.mjs  # build /services hub (clones web-design-sabah.html)
node _build/blog-cta.mjs         # inject contextual blog->service CTAs
node _build/relativize.mjs       # root-absolute -> relative paths (MUST list every page)
node _build/cleanup.mjs          # strip schema offers + remove em dashes site-wide
node _build/validate.mjs         # check (run BEFORE relativize for meaningful output)
```

Notes:
- Owner/contact constants live at the top of `seo-fix.mjs`, `build-pages.mjs`,
  `build-services-hub.mjs`. Change them in all three.
- `build-pages.mjs` still contains pricing tier arrays — they're **dead data** the redefined
  `pricingHtml()` ignores. Safe to ignore or delete.
- `tweaks.mjs` is **deprecated** (its nav + byline fixes were folded into `seo-fix.mjs`).
- `validate.mjs` only checks root-absolute refs, so it reports 0/0 after `relativize`. To
  validate links, run it between `blog-cta` and `relativize`.

---

## 5. SEO fixes applied (from the audit)

Schema/JSON-LD on every page (Organization/LocalBusiness, WebSite, FAQPage, BlogPosting w/ two
authors, BreadcrumbList, Service, AboutPage, ContactPage, Blog, CollectionPage) · 5 service +
hub + about + contact + blog index · hreflang (en-my/en-au/x-default) + `lang=en-MY` · absolute
canonicals · og:image everywhere · Lorem ipsum removed · pseudonym bylines → Bing & Liang ·
WebFont.js + sitewide reCAPTCHA + dead Webflow GA-proxy removed, direct Lato `<link>` · enriched
NAP footer · robots.txt deduped, sitemap.xml + lastmod, llms.txt.

---

## 6. Next actions (human-only)

- [ ] **DNS to apex** (§2) — only thing between the clone and `sabahwebs.com`. Owners' call.
- [ ] **Contact form** — forms POST to `https://formspree.io/f/REPLACE_FORM_ID`. Create a free
      Formspree form and swap the ID in `contact.html` + `index.html`. (WhatsApp/phone/email work now.)
- [ ] **`hello@sabahwebs.com`** mailbox (optional; currently using the working Gmail).
- [ ] **Google Business Profile** — create + verify (Service-Area Business, KK/Sandakan/Tawau/Sabah).
- [ ] **Security headers + HSTS** — not possible on GitHub Pages; add via Cloudflare if re-fronted.
- [ ] **Real testimonials** — replace the neutral placeholder once collected.
- [ ] **Confirm copy** — service-page wording, hub copy, and the homepage "Our services" blurb.

---

## 7. Environment / gotchas

- **GitHub auth:** no `gh` CLI / no `GITHUB_TOKEN`, but Git Credential Manager holds a valid
  token for account **`fyb27`** — `git push` over HTTPS just works; pull the token via
  `git credential-manager get` for REST API calls (repo create, Pages enable/disable, cname clear).
  `git config --global user.name/email` are empty; set per-repo.
- **Webflow forms** are neutralised (the `w-form` class is stripped so webflow.js stops
  hijacking) and pointed at Formspree. `webflow.js` is still loaded for the mobile nav + FAQ accordion.
- **Fonts:** direct Google Fonts `<link>` (Lato 400/700/900 + 400 italic). Self-host the woff2
  for max performance if desired.
- **Tracking kept:** Google Analytics (G-BK1FVF46K5) + Ahrefs analytics on all pages.
- **Audit source:** `Z:\sites\SEO dashboard\reports\sabahwebs-seo-audit-2026-06-08.{html,pdf}`.
