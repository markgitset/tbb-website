# Texas Bible Bowl website

Hugo static site, deployed via GitHub Actions to GitHub Pages at the custom domain
**https://texasbiblebowl.org**.

## Local development
- **Start the dev server with an explicit local baseURL:**
  ```
  hugo server --baseURL "http://localhost:1313/" --appendPort=false
  ```
  A plain `hugo server` does **not** work well here: because `canonifyURLs = true`, Hugo
  bakes the configured **production** baseURL (`https://texasbiblebowl.org/`) into every link,
  so clicking anything locally jumps to the live site. The explicit flag keeps links local.
- Reproduce the production build locally: `hugo --gc --minify --baseURL "https://texasbiblebowl.org/"`.
  Do **not** run this foreground build while the dev server is running — it can flip the running
  server back to the production baseURL. Stop the server first, or run it in a separate checkout.

## Deploy
- Push to `master` triggers `.github/workflows/deploy.yml`, which builds with Hugo and
  publishes to GitHub Pages. The build's baseURL comes from the Pages config (`configure-pages`),
  not from `hugo.toml`.
- Custom domain `texasbiblebowl.org` is set in repo Pages settings (apex A records → GitHub IPs
  at Namecheap; **Enforce HTTPS** on; domain ownership verified via a `_github-pages-challenge-markgitset`
  TXT record). A `static/CNAME` file is also published.

## Conventions & gotchas (these have caused real breakage)
- **Keep Hugo versions in sync.** `HUGO_VERSION` in `deploy.yml` must match local `hugo version`
  (currently 0.163.3). A newer-than-CI feature (e.g. `hugo.Data`) builds locally but fails CI.
- **Static assets in templates:** reference with `{{ .Site.BaseURL }}css/custom.css` (no leading
  slash after BaseURL). Do **not** use `absURL`/`relURL` — they break on localhost / the Pages base.
  In `range` loops use `{{ $.Site.BaseURL }}`.
- **`canonifyURLs = true` in `hugo.toml` is required — do not remove it.** Content is authored with
  root-relative paths (raw `<img src="/images/...">`, markdown `[](/uploads/...)`), which Hugo does
  not template; canonifyURLs rewrites them with the build's baseURL so they resolve correctly.
- **After changing the custom domain or toggling Enforce HTTPS, re-run the deploy** (`gh workflow run
  deploy.yml --ref master`). URLs are baked at build time, so they must be regenerated. If Enforce
  HTTPS is off, the build's baseURL is `http://` → mixed-content blocking on the HTTPS site.

## Annual maintenance (design priority)
This site is updated at least once a year — the **event date** and **study material** change
every year (material runs on a ~10-year cycle, but the **theme** changes each time even when the
material cycles back). **Design every change to minimize yearly update effort:** parameterize the
things that change so they live in ONE place. Yearly-changing values belong in `hugo.toml`
`[params]` (`eventYear`, `eventDateRange`, `eventTheme`, `eventScripture`) and are composed in
templates (e.g. `{{ printf "%s, %s" .Site.Params.eventDateRange .Site.Params.eventYear }}`), never
hardcoded or duplicated. Goal: a new year = edit a few params, nothing else. When content must
restate a date/year/material, flag it rather than scattering copies.

## Content structure
- Pages live under `content/<section>/`. The nav menu is defined in `hugo.toml` (`[[menus.main]]`);
  menu URLs must match the actual section paths.
- **Nav conventions:** the top nav is intentionally kept to ~6 items. Photos is a child of
  **About** (not top-level); the year galleries are listed on the `/photos/` landing page, not in
  the menu. **Contact is footer-only** (in `footer.html`), deliberately not in the top nav. The nav
  template (`partials/nav.html`) auto-injects a "{Section} overview" link at the top of every
  dropdown (so the section landing page is reachable on touch), highlights the active section, and
  renders any menu item named `Donate` as a gold button — add new dropdown children via
  `[[menus.main]]` only.
- Photo galleries are data-driven: `data/photos/<year>.json` lists filenames, with the image files
  in `static/uploads/`. The `photos/single.html` template renders them.
- Brand: navy `#1a3a5c` / gold `#c9952a`; fonts Fraunces (headings) + Inter (body); styles in
  `static/css/custom.css`. **Bootstrap Icons** (`<i class="bi bi-…">`) are loaded in `baseof.html`.

## Site-wide features (how they're wired)
- **Breadcrumbs:** `partials/breadcrumbs.html`, included by `single.html`, `list.html`, and
  `sitemap.html`. Driven by `.Ancestors` — no per-page config needed.
- **Search:** client-side, no server. `[outputs] home = [HTML, RSS, JSON]` in `hugo.toml` +
  `layouts/index.json` emit `/index.json` (one entry per content/section page, search page
  excluded). `/search/` (`content/search.md` + `layouts/_default/search.html`) queries it with
  **Fuse.js** via `static/js/search.js` (live filtering, `?q=` deep link). The navbar search box
  submits to `/search/`. If you add a page type that shouldn't be indexed, exclude it in
  `layouts/index.json`.
- **HTML sitemap:** `/sitemap/` (`content/sitemap.md` + `layouts/_default/sitemap.html`) lists every
  section and standalone page; linked from the footer. Distinct from the machine `sitemap.xml`.
- **"Start here" emphasis:** set `startHere: "<page-slug>"` in a section's `_index.md` to badge that
  child's card on the section landing page (`list.html`). Opt-in; e.g. Details → `how-it-works`.
- **Social/SEO:** Open Graph + Twitter card meta are in `baseof.html` (per-page title/description,
  `hero.jpg` as the share image).

## Source assets
- Original Weebly site (content/images to migrate from) is local-only at
  `/home/mark/ws/tbb-website-weebly/` — not on the live site.
