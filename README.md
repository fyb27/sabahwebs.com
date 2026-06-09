# sabahwebs.com

Static site for **SabahWebs** (Happy Codes) — web design & SEO agency in Sabah, Malaysia.
Migrated off Webflow hosting to a self-hosted static site for GitHub Pages, with the
[SEO audit](https://sabahwebs.com) fixes baked in.

---

## What this is

A fully self-contained static mirror of sabahwebs.com — all HTML, CSS, JS, fonts and
images are local (no Webflow CDN dependency). Built to be served by **GitHub Pages** on the
custom domain `sabahwebs.com`.

```
/                         homepage
/blog/<slug>.html         10 existing blog posts (extensionless URLs preserved)
/blog/                    NEW blog index (was a 404)
/web-design-sabah         NEW service page
/seo-services-sabah       NEW service page
/web-design-kota-kinabalu NEW location page
/ecommerce-website-sabah  NEW service page
/webflow-website-sabah    NEW service page
/about                    NEW about page (founders: Bing & Liang)
/contact                  NEW contact page (WhatsApp/phone + Formspree form)
/404.html                 NEW branded 404
/assets/**                all localized CSS/JS/fonts/images
/assets/site/custom.css   custom styles for new pages + enriched footer
sitemap.xml robots.txt llms.txt CNAME .nojekyll
_build/                   build tooling (gitignored, not deployed)
```

## Deploying on GitHub Pages

1. Push this repo to GitHub (already done if you're reading this on github.com).
2. **Settings → Pages →** Source: *Deploy from a branch*, Branch: `main` / root (`/`).
3. The `CNAME` file sets the custom domain to `sabahwebs.com`. In your DNS, point the
   apex domain at GitHub Pages:
   - `A` records → `185.199.108.153`, `185.199.109.153`, `185.199.110.153`, `185.199.111.153`
   - (or `ALIAS`/`ANAME` to `fyb27.github.io` if your DNS supports it)
   - `www` → `CNAME` → `fyb27.github.io`
4. Enable **Enforce HTTPS** once the cert provisions.

`.nojekyll` is present so GitHub Pages serves everything as-is (no Jekyll processing).
Extensionless URLs like `/blog/ai-seo-malaysia` resolve to `blog/ai-seo-malaysia.html`.

## SEO fixes applied (from the 8 Jun 2026 audit)

- ✅ **Schema/JSON-LD** on every page: `ProfessionalService`+`LocalBusiness` org, `WebSite`,
  `FAQPage`, `BlogPosting` (2 authors), `BreadcrumbList`, `Service`, `AboutPage`,
  `ContactPage`, `Blog`.
- ✅ **5 service/location pages + About + Contact** built (the audit's #1 growth blocker).
- ✅ **Blog index** created (fixes the `/blog` 404).
- ✅ `lang="en-MY"` + **hreflang** (`en-my`, `en-au`, `x-default`) on all pages.
- ✅ **Canonical** tags fixed to absolute URLs.
- ✅ **og:image / og:url / twitter:image** added on every page (none existed before).
- ✅ **Lorem ipsum** testimonials → neutral placeholder.
- ✅ **Pseudonym bylines** ("Curious/Honest/Happy Explorer") → real authors (Bing & Liang).
- ✅ **Performance:** removed render-blocking WebFont.js (18 weights) → direct Lato `<link>`
  (4 weights, `display=swap`); removed sitewide reCAPTCHA and the dead Webflow GA-proxy.
- ✅ **Enriched footer** with real NAP (phone, WhatsApp, email, service areas) sitewide.
- ✅ `robots.txt` duplicate `Sitemap:` removed; `sitemap.xml` rebuilt with all pages + `lastmod`.
- ✅ `llms.txt` added for AI-search/GEO.

## ⚠️ Still needs a human (can't be done in code)

1. **Contact form backend** — forms point to `https://formspree.io/f/REPLACE_FORM_ID`.
   Create a free [Formspree](https://formspree.io) form and replace `REPLACE_FORM_ID`
   in `contact.html` and `index.html`. (WhatsApp/phone/email already work everywhere.)
2. **`hello@sabahwebs.com`** — audit recommends a branded email; currently using the
   working `happycodesmy@gmail.com`. Set up the mailbox, then swap it in if desired.
3. **Pricing numbers** in the service pages are realistic Malaysian starting points —
   confirm/adjust them to your actual rates.
4. **Google Business Profile** — create & verify as a Service-Area Business (now that you
   have a phone number). Highest-impact local action.
5. **Security headers + HSTS** — GitHub Pages can't set custom headers. If you front the
   site with Cloudflare (as before), add them there.
6. **Real testimonials** — replace the neutral placeholder once you collect client quotes.

Generated with the build scripts in `_build/` (re-runnable: `mirror → seo-fix →
extract-chrome → build-pages`).
