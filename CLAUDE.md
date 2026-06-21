# Texas Bible Bowl website

Hugo static site, deployed via GitHub Actions to GitHub Pages at the custom domain
**https://texasbiblebowl.org**.

## Local development
- `hugo server` → preview at http://localhost:1313 (live reload).
- Reproduce the production build locally: `hugo --gc --minify --baseURL "https://texasbiblebowl.org/"`.

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
- Photo galleries are data-driven: `data/photos/<year>.json` lists filenames, with the image files
  in `static/uploads/`. The `photos/single.html` template renders them.
- Brand: navy `#1a3a5c` / gold `#c9952a`; fonts Fraunces (headings) + Inter (body); styles in
  `static/css/custom.css`.

## Source assets
- Original Weebly site (content/images to migrate from) is local-only at
  `/home/mark/ws/tbb-website-weebly/` — not on the live site.
