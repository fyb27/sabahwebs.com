# HANDOFF — sabahwebs.com (Webflow → GitHub Pages migration + SEO)

**Last updated:** 2026-06-09
**Subject site:** sabahwebs.com — web design + SEO agency, Sabah Malaysia (+ Australia secondary)
**Owners:** Bing & Liang (two partners) · WhatsApp/phone **+60 16-843 0891** · happycodesmy@gmail.com
**Repo:** https://github.com/fyb27/sabahwebs.com (public, branch `main`)
**Live now:** https://fyb27.github.io/sabahwebs.com/  *(direct, no redirect)*
**Future home:** https://sabahwebs.com *(after DNS switch — still on Webflow/Cloudflare today)*
**Local working copy:** `Z:\sites\sabahwebs happycodes\sabahwebs`

---

## 1. What was done

Migrated sabahwebs.com **off Webflow hosting** into a self-contained static site, pushed to
GitHub, deployed on GitHub Pages, and implemented the fixes from the
`sabahwebs-seo-audit-2026-06-08` report (in `Z:\sites\SEO dashboard\reports\`).

- **Cloned** all 11 original pages (homepage + 10 blog posts) and **129 assets** (CSS, JS,
  jQuery, webflow.js, fonts, images, favicons). All references rewritten to local paths —
  **zero Webflow CDN dependency**. Original URLs preserved (extensionless, e.g. `/blog/ai-seo-malaysia`).
- **Built 9 new pages** (full SEO copy): 5 service/location pages + About + Contact + blog
  index + branded 404.
- **Applied the SEO audit fixes** (see §5).
- **Pushed + deployed:** repo `fyb27/sabahwebs.com`, GitHub Pages serving from `main` root.
  Custom domain was removed (per owner request) so the github.io URL serves directly with
  no redirect; asset/link paths are relative so the site works at both the github.io
  subpath and the apex domain.

---

## 2. Current state

| Thing | Status |
|---|---|
| Clone off Webflow | ✅ Done — fully self-hosted |
| Pushed to GitHub | ✅ `fyb27/sabahwebs.com`, `main`, 154 files |
| GitHub Pages | ✅ Live & verified (homepage, CSS, service pages, blog posts all HTTP 200) |
| SEO audit code fixes | ✅ Done (§5) |
| DNS to apex domain | ⏳ Owners will do later |
| Contact form backend | ⏳ Formspree placeholder, needs real ID |

---

## 3. Repo structure

```
index.html                       homepage
404.html                         branded 404 (GitHub Pages custom 404)
about.html  contact.html         new pages
web-design-sabah.html            new service page
seo-services-sabah.html          new service page
web-design-kota-kinabalu.html    new location page
ecommerce-website-sabah.html     new service page
webflow-website-sabah.html       new service page
blog/index.html                  NEW blog index (was a 404)
blog/<slug>.html                 10 original posts (fixed)
assets/**                        all localized CSS/JS/fonts/images
assets/site/custom.css           styles for new pages + enriched footer
sitemap.xml robots.txt llms.txt  SEO/root files
.nojekyll                        serve files as-is (no Jekyll)
README.md HANDOFF.md
_build/                          build tooling (gitignored, NOT deployed)
```

---

## 4. Build pipeline (how to regenerate)

All tooling is in `_build/` (Node ESM, run from repo root). Re-runnable in order:

```
node _build/mirror.mjs          # re-download 11 pages + assets from live Webflow
node _build/seo-fix.mjs         # apply sitewide SEO/migration fixes to the 11 pages
node _build/extract-chrome.mjs  # snapshot navbar/footer/scripts into _build/chrome.json
node _build/build-pages.mjs     # build the 9 new pages
node _build/blog-cta.mjs        # inject contextual blog→service CTAs
node _build/relativize.mjs      # convert /asset paths to relative (subpath + apex safe)
node _build/validate.mjs        # check all local assets + internal links resolve
```

> ⚠️ The pipeline rewrites the 11 source HTML files in place and is **not** idempotent
> (re-running `seo-fix` twice double-injects). Always start from a fresh `mirror`.
> Key constants (owners, phone, email) live at the top of `seo-fix.mjs` and `build-pages.mjs`.

---

## 5. SEO fixes applied (from the 2026-06-08 audit)

- ✅ **Schema/JSON-LD** on every page: `ProfessionalService`+`LocalBusiness` org,
  `WebSite`, `FAQPage` (5 Q&A from homepage), `BlogPosting` (2 authors + dates + hero image),
  `BreadcrumbList`, `Service`, `AboutPage`, `ContactPage`, `Blog`.
- ✅ **5 service/location pages + About + Contact** (the audit's #1 growth blocker).
- ✅ **Blog index** created → fixes the `/blog` 404.
- ✅ `lang="en-MY"` + **hreflang** (`en-my` / `en-au` / `x-default`).
- ✅ **Canonical** tags fixed to absolute URLs.
- ✅ **og:image / og:url / og:site_name / og:locale / twitter:image** on every page (none existed).
- ✅ **Lorem ipsum** testimonials → neutral placeholder.
- ✅ **Pseudonym bylines** ("Curious/Honest/Happy Explorer") → real authors (Bing & Liang).
- ✅ **Performance:** removed render-blocking WebFont.js (18 weights) → direct Lato `<link>`
  (4 weights, `display=swap`); removed sitewide reCAPTCHA; removed dead Webflow GA-proxy script.
- ✅ **Enriched footer** with real NAP (phone, WhatsApp, email, service areas) sitewide.
- ✅ `robots.txt` duplicate `Sitemap:` removed; `sitemap.xml` rebuilt with all 19 URLs + `lastmod`.
- ✅ `llms.txt` added (AI search / GEO).
- ✅ Webflow form submit hijack neutralized; forms rewired to a Formspree endpoint placeholder.

---

## 6. Next actions (human-only — can't be done in code)

- [ ] **Switch DNS to the apex** (see §7) — the only thing between this and `sabahwebs.com`.
- [ ] **Activate the contact form:** create a free [Formspree](https://formspree.io) form and
      replace `REPLACE_FORM_ID` in `contact.html` **and** `index.html`. (WhatsApp/phone/email
      already work everywhere.)
- [ ] **Confirm pricing numbers** in the 5 service pages — they're realistic Malaysian
      starting points, not your confirmed rates. Edit the `pricingHtml(...)` blocks (or the
      built HTML directly).
- [ ] **Google Business Profile** — create & verify as a Service-Area Business (primary
      category "Web Designer"; areas KK / Sandakan / Tawau / Sabah). Highest local-ranking action.
- [ ] **`hello@sabahwebs.com`** — set up the mailbox, then swap it in if you want it as the
      public email (currently using the working `happycodesmy@gmail.com`).
- [ ] **Real testimonials** — replace the neutral placeholder once you collect client quotes.
- [ ] **Security headers + HSTS** — GitHub Pages can't set custom headers. If you front the
      site with Cloudflare again, add them there (audit flagged this as HIGH).
- [ ] *(From audit Phase 3, longer-term)* move client content (Orou Sapulot ×4, scaffolding,
      clinic) to `/projects/[client]` case studies; build local citations (Clutch/GoodFirms);
      request Google reviews from the 4 named clients.

---

## 7. Going live on sabahwebs.com (DNS)

1. In the repo: **Settings → Pages → Custom domain →** enter `sabahwebs.com` and Save
   (this recreates the `CNAME`). The relative paths already work at the apex, so nothing
   else changes.
2. At your DNS host (currently Cloudflare):
   - `A  @ → 185.199.108.153 / 185.199.109.153 / 185.199.110.153 / 185.199.111.153`
   - `CNAME  www → fyb27.github.io`
   - Remove the old Webflow DNS records.
   - If using Cloudflare proxy (orange cloud), set SSL/TLS mode to **Full**.
3. Once DNS resolves, tick **Enforce HTTPS** in Pages settings (cert auto-provisions).

> The canonical/og/hreflang/JSON-LD URLs already point to `https://sabahwebs.com`, so search
> engines treat the apex as the canonical home and won't index the github.io URL.

---

## 8. Environment notes / gotchas

- **GitHub auth:** no `gh` CLI and no `GITHUB_TOKEN` env on this machine, but Git Credential
  Manager holds a valid token for account **`fyb27`** — `git push` over HTTPS just works, and
  the REST API token can be pulled via `git credential-manager get` (used to create the repo
  + enable Pages). `git config --global user.name/email` are empty; set per-repo.
- **Relative paths:** `relativize.mjs` is what lets the site work at `github.io/sabahwebs.com/`.
  If you ever re-run the build and forget it, the site breaks at the subpath (root-absolute
  `/assets` → 404). It's safe at the apex too, so just always run it last.
- **Webflow forms** POST to Webflow's servers via `webflow.js`; off-platform they don't submit.
  We removed the `w-form` wrapper class so `webflow.js` stops hijacking them and pointed them
  at Formspree. `webflow.js` is still loaded for the mobile nav toggle + FAQ accordion.
- **Fonts:** now a direct Google Fonts `<link>` (Lato 400/700/900 + 400 italic). Self-hosting
  the woff2 files would remove the last third-party connection if you want max performance.
- **Tracking kept:** Google Analytics (G-BK1FVF46K5) + Ahrefs analytics are still wired on all
  pages, including the new ones.
- **PDF/audit source:** the original audit + this project's first handoff live in
  `Z:\sites\SEO dashboard\reports\` (`sabahwebs-seo-audit-2026-06-08.{html,pdf}`, `HANDOFF.md`).
