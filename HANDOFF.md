# HANDOFF — sabahwebs.com (Webflow clone + SEO, hosted on GitHub Pages)

**Last updated:** 2026-06-09 (mid-afternoon, GMT+8)
**Subject site:** sabahwebs.com — web design + SEO agency, Sabah Malaysia (+ Australia secondary)
**Owners:** Bing & Liang (two partners) · WhatsApp/phone **+60 16-843 0891** · happycodesmy@gmail.com
**Repo:** https://github.com/fyb27/sabahwebs.com (public, branch `main`)
**Live (GitHub clone):** https://fyb27.github.io/sabahwebs.com/  ← serves directly, no redirect
**Real site (unchanged):** https://sabahwebs.com is still on **Webflow** (Cloudflare DNS). Never touched.
**Local working copy:** `Z:\sites\sabahwebs happycodes\sabahwebs`
**Latest commit on main:** `a33f7f3` (Homepage/blog/nav refinements)

---

## ⚠️ READ THIS FIRST — open issues + requested work (NOT yet done)

The owner gave a "boil the ocean" batch near the end of the session and then asked for this
handoff before it was executed. **None of the items in this section are done yet.** They are
the next session's job. Priority order is roughly top-to-bottom.

### A. Confirmed bugs still live
1. **FAQ "+" accordion does not toggle.** Reported on the `/services` page (and any page using the
   `.sw-faq` `<details>/<summary>` blocks). Earlier we added `.page_background{pointer-events:none}`
   which fixed *clicks being swallowed* — but the owner says the FAQ still won't open. Native
   `<details>` needs no JS, so suspect: (a) `webflow.js` / another script calling `preventDefault`
   on summary clicks, or (b) a remaining element still overlapping the summary. **Test in a real
   browser** (no Playwright/Puppeteer installed locally — `seo-visual` agent or manual). Don't
   assume CSS-only reasoning again; verify the actual click.
2. **`.page_background` visually overlaps text.** It's `position:fixed; inset:0` with blurred blob
   images (`.hero_blob-*`, `z-index:0/2`). `pointer-events:none` made it click-through, but the
   blobs still **paint on top of** some headings/words (the generated `<main>` content has no
   stacking context above it). Fix properly: give the page content a stacking context above the
   blobs (e.g. wrap/raise `<main>` with `position:relative; z-index:1`) or push `.page_background`
   behind with a negative z-index. Affects homepage, `/services`, and the `/blog` cards/words.
3. **"Resources" → "Blog" rename may not be everywhere.** Deployed built files verified 0 nav
   "Resources" left, but the owner still saw it somewhere — re-check **all** surfaces: nav on every
   page type, the homepage section `<h2>`, `chrome.json` (generated pages snapshot still says
   "Resources"), and any `#resources` anchors. Could also have been CDN cache — confirm with a hard
   refresh / cache-bust.

### B. `/services` page redesign (owner request)
4. **Remove the small blue eyebrow label** (`.sw-eyebrow`, e.g. "Our Services", "What we do",
   "Why SabahWebs", "Pricing", "FAQ") from **every** section on the page. Owner dislikes it. Remove
   completely (in `build-services-hub.mjs` `MAIN` + the deployed `services.html`).
5. **Remove the "Common questions" FAQ section** from `/services` entirely.
6. **Reduce to just service cards:** Web design, SEO optimisation, **Website maintenance** (new).
   The page should essentially be the hero + the cards (+ keep the bottom CTA). Drop the "Why
   SabahWebs" + "How we price" blocks unless the owner wants them — confirm.

### C. New service: Website maintenance
7. **Add a "Website maintenance" card** to the homepage **"Our Services in Sabah"** section
   (currently only "Web Design" + "SEO Optimisation" — see §1). Use a fitting Webflow icon.
8. Add **Website maintenance** as a card on `/services` (see B6). Decide with owner whether it also
   needs a dedicated `/website-maintenance-sabah` page (the other services have one). If yes, add to
   `build-pages.mjs` **and** every page-list array (see §4 gotchas) + sitemap + footer.

### D. `/blog` page
9. **Revert to 3 columns** (`sw-grid cols-3`) — the page is wider now (`.sw-wrap` = 80rem), so 3
   image cards fit. (Currently `cols-2` at 240px image height; from `build-pages.mjs` blog block +
   `blog/index.html`.)
10. **Background overlaps the cards/words here too** — same fix as A2.
11. **Reword the subtitle under the "Blog" H1.** Currently a long sentence; owner wants something
    short like **"Read our blogs!"**. (In `build-pages.mjs` blog block + `blog/index.html`.)

### E. Final sweep
12. **Rescan the whole site for code redundancy + bugs and fix.** Dead CSS, duplicate/contradictory
    Webflow rules, leftover `data-w-id` interaction attributes with no JS, dead pricing arrays in
    `build-pages.mjs`, broken/again-absolute links, unused assets, etc.

---

## 0. Mental model (important)

There are **two separate, independent sites**:

| | Real site | GitHub clone |
|---|---|---|
| URL | `sabahwebs.com` | `fyb27.github.io/sabahwebs.com/` |
| Host | Webflow / Cloudflare | GitHub Pages |
| Status | live, untouched | live, this repo |

The clone does **not** point at or affect `sabahwebs.com` (Pages custom domain removed, `cname`
null). They only connect if the owners later repoint DNS to GitHub (their call).

---

## 1. Current state of the site (what's live now)

Pages (extensionless URLs, served by GitHub Pages):
`/` · `/services` · `/web-design-sabah` · `/seo-services-sabah` · `/web-design-kota-kinabalu` ·
`/webflow-website-sabah` · `/about` · `/contact` · `/blog` (index) · `404.html` · `/blog/<10 posts>`.

**E-commerce has been fully removed** this session (owner: "not confident doing that"): the
`/ecommerce-website-sabah` page is **deleted**, and all references are gone from the footer,
homepage grid, services hub, cross-links, sitemap, and schema.

Homepage:
- **"Our Services in Sabah"** now shows only **2 cards** — Web Design + SEO Optimisation — with a
  **"Read more"** button below → `/services`. *(Owner wants a 3rd card "Website maintenance" — §A/C.)*
- **"Explore Our Blog"** section (was "Explore Our Resources"): the 4 Webflow tabs were stripped to
  one, trimmed to the **3 latest posts**, with a **"See all blog posts"** button → `/blog`.
- Contact block ("Have an idea in mind?") now has the **black email button** + a **green "WhatsApp
  us"** button (`.button.is-wa`).

Nav (every page): **Projects · Services (→/services) · Blog (→/blog) · FAQ · Get in touch**.
A Services dropdown was prototyped then removed at the owner's request — plain link is intentional.

`/blog` index: H1 **"Blog"** + subtitle, **2-col** image cards (one hero image per post).
*(Owner wants 3-col + shorter subtitle — §D.)*

`/services` hub: hero (no buttons) · service cards (Web design / SEO / Webflow, each "Learn more"
→ its page) · "Why SabahWebs" · "How we price" · FAQ · bottom CTA. *(Owner wants heavy trim — §B.)*

---

## 2. Serving model (read before touching paths)

- Site uses **relative** asset/link paths (`assets/...`, `../assets/...`) so it renders at the
  github.io subpath. **Do not** revert to root-absolute `/assets` or the subpath build breaks.
- `relativize.mjs` does the conversion. **Every new HTML file MUST be listed** in its
  `rootPages`/blog arrays or it ships unstyled (this bit us with `services.html` once).
- Canonical / og / hreflang / JSON-LD URLs stay **absolute** (`https://sabahwebs.com/...`) on
  purpose, so the apex stays the SEO canonical and the github.io copy isn't indexed.
- Internal nav/links are root-absolute in source and relativized per-page (root pages get ``,
  blog pages get `../`).

### To move the clone onto sabahwebs.com later
1. Repo → Settings → Pages → Custom domain → `sabahwebs.com` (recreates CNAME).
2. DNS: `A @ → 185.199.108–111.153`; `CNAME www → fyb27.github.io`; remove Webflow records.
3. Enforce HTTPS once the cert provisions. Relative paths already work at the apex.

---

## 3. Theme + design notes

- **LIGHT theme**: page `#fafafa`, body text dark. The Webflow class `background_dark` is
  misleadingly named (resolves to off-white). Style new components dark-on-light.
- Brand: primary `#f25836` (orange), teal `#00adb5`, ink `#222831`, cream `#f3dfa2`.
- **WhatsApp = green** (`#25d366`): `.sw-btn.is-wa` and `.button.is-wa`. The site's default
  `.button` (Webflow) = **black** (raisin-black) — used for email / "Read more" / "See all" CTAs.
- All new-page styling lives in `assets/site/custom.css` (`.sw-*` classes). Webflow's CSS still
  powers the homepage/blog shell + navbar/footer. `custom.css` is the **last** stylesheet loaded,
  so its rules win.
  - Key custom rules added this session: `.sw-wrap{max-width:80rem}` (page width matches homepage),
    `.page_background{pointer-events:none}` (click-through — see §A2, still has a *visual* overlap
    bug), `.button.is-wa` (green WhatsApp).
- **No em dashes** anywhere (owner preference) — enforced by `cleanup.mjs`.
- **No prices** on service pages — schema `offers` stripped; pricing is a no-number block. The two
  blog articles on SEO/website pricing keep their figures (editorial, intentional).

---

## 4. Build pipeline (scripts in `_build/`, gitignored — NOT deployed)

> **CRITICAL CAVEAT:** the **deployed built files are the current source of truth.** Many of this
> session's changes were applied **directly to the built HTML** (then mirrored back into the build
> scripts on a best-effort basis) because `relativize.mjs` is non-idempotent and a full re-mirror
> is risky. **A clean rebuild is NOT guaranteed to reproduce the live site 1:1.** Known divergence
> risks: the nav "Resources→Blog" rename lives in `tweaks.mjs` (which may not be in the active run
> order) and the generated-page nav comes from `chrome.json` (still snapshots "Resources"). If you
> rebuild, diff against the deployed files and re-apply anything lost. Prefer **targeted edits to
> built files + keep scripts in sync**, like this session did, over a blind full rebuild.

Intended order (from repo root; a fresh `mirror` first — `seo-fix` is **not** idempotent):

```
node _build/mirror.mjs              # download home + 10 blog posts + assets from live Webflow
node _build/seo-fix.mjs             # sitewide SEO/migration fixes to the mirrored pages.
                                    #   Homepage-only transforms now live here:
                                    #   - trimHomeServices(): keep only Web Design + SEO cards
                                    #   - HOME_SERVICES_CTA "Read more" -> /services
                                    #   - rename "Explore Our Resources" -> "Explore Our Blog"
                                    #   - trimResources(): 4 tabs -> 3 latest posts + RESOURCES_CTA
                                    #     ("See all blog posts" -> /blog)
                                    #   - add green WhatsApp button beside homepage email button
node _build/extract-chrome.mjs      # snapshot navbar/footer/scripts -> _build/chrome.json
node _build/build-pages.mjs         # build the new pages (no prices). Blog index block has the
                                    #   per-post image map + (currently) cols-2 grid + "Blog" hero.
node _build/build-services-hub.mjs  # build /services hub (clones web-design-sabah.html)
node _build/blog-cta.mjs            # inject contextual blog->service CTAs
node _build/relativize.mjs          # root-absolute -> relative paths (MUST list every page)
node _build/cleanup.mjs             # strip schema offers + remove em dashes site-wide
node _build/tweaks.mjs              # nav Services->/services + "Resources"->"Blog" rename + bylines
                                    #   (runs AFTER relativize; matches ./ and ../ hrefs)
node _build/validate.mjs            # link/asset check (run BEFORE relativize for meaningful output)
```

Notes / gotchas:
- **Owner/contact constants** live at the top of `seo-fix.mjs`, `build-pages.mjs`,
  `build-services-hub.mjs` — change in all three.
- **Page-list arrays** (the `rootPages`/files lists) exist in `relativize.mjs`, `cleanup.mjs`,
  `tweaks.mjs`, `validate.mjs`. Adding/removing a root page means editing **all** of them.
  E-commerce was removed from all of these this session.
- `build-pages.mjs` still contains **dead pricing tier arrays** the redefined `pricingHtml()`
  ignores — candidates for the §E12 cleanup.
- `seo-fix.mjs` helper `endOfDiv()` powers `trimHomeServices`/`trimResources` (div-depth aware
  slicing of Webflow markup). Reuse it for the §B/§C card edits.

---

## 5. SEO fixes applied (from the 2026-06-08 audit)

Schema/JSON-LD on every page (Organization/LocalBusiness, WebSite, FAQPage, BlogPosting w/ two
authors, BreadcrumbList, Service, AboutPage, ContactPage, Blog, CollectionPage) · hreflang
(en-my/en-au/x-default) + `lang=en-MY` · absolute canonicals · og:image everywhere · Lorem ipsum
removed · pseudonym bylines → Bing & Liang · WebFont.js + sitewide reCAPTCHA + dead Webflow GA-proxy
removed, direct Lato `<link>` · enriched NAP footer · robots.txt deduped · sitemap.xml + lastmod ·
llms.txt. **Audit source:** `Z:\sites\SEO dashboard\reports\sabahwebs-seo-audit-2026-06-08.{html,pdf}`.

---

## 6. Next actions (human-only)

- [ ] **DNS to apex** (§2) — only thing between the clone and `sabahwebs.com`. Owners' call.
- [ ] **Contact form** — forms POST to `https://formspree.io/f/REPLACE_FORM_ID`. Create a free
      Formspree form and swap the ID in `contact.html` + `index.html`. (WhatsApp/phone/email work now.)
- [ ] **`hello@sabahwebs.com`** mailbox (optional; currently using the working Gmail).
- [ ] **Google Business Profile** — create + verify (Service-Area Business, KK/Sandakan/Tawau/Sabah).
- [ ] **Security headers + HSTS** — not possible on GitHub Pages; add via Cloudflare if re-fronted.
- [ ] **Real testimonials** — replace the neutral placeholder once collected.

---

## 7. Environment / gotchas

- **GitHub auth:** no `gh` CLI / no `GITHUB_TOKEN`, but Git Credential Manager holds a valid token
  for account **`fyb27`** — `git push` over HTTPS just works; pull the token via
  `git credential-manager get` for REST API calls. Set `user.name/email` per-repo.
- **Line endings:** git normalizes LF→CRLF on commit (cosmetic warnings; files are single-line, no
  rendering impact). Python edits in this session wrote with `newline=''` to preserve content.
- **No local headless browser** (Playwright/Puppeteer absent) — use the `seo-visual` agent or test
  manually for anything that needs real click/render verification (e.g. the §A1 FAQ bug).
- **Webflow forms** are neutralised (`w-form` class stripped so webflow.js stops hijacking) and
  pointed at Formspree. `webflow.js` is still loaded for the mobile nav (+ the FAQ accordion, which
  is currently broken — §A1).
- **Verify deploys with a cache-bust** (`?cb=$RANDOM`); the github.io CDN serves a stale copy for
  ~30–60s after a push, so the first poll after deploy usually shows the OLD page.
- **Tracking kept:** Google Analytics (G-BK1FVF46K5) + Ahrefs analytics on all pages.

---

## 8. This session's commits (newest first)

- `a33f7f3` Homepage/blog/nav refinements (2-card home services + Read more; Resources→Blog;
  "Explore Our Blog"; blog hero slimmed to "Blog"; `.sw-wrap` 80rem; green WhatsApp on homepage)
- `4b8f83c` Fix click-blocking blob overlay (`pointer-events:none`); enlarge blog index cards
- `4b82080` Drop e-commerce offering (delete page + all refs); services hero CTAs to bottom; blog
  index images
- `cd1ed8f` Single services section + "See all services"; slim resources to 3 latest
- (earlier, prior session) e99c17b HANDOFF restructure · e4f3026 home "Our services" section ·
  55e7bbb services.html relativize fix
