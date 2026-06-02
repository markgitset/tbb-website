# Texas Bible Bowl Website

Hugo static site for [texasbiblebowl.org](https://texasbiblebowl.org), hosted on GitHub Pages.

## Prerequisites

- [Hugo Extended](https://gohugo.io/installation/) v0.128+
- Git

## Local Development

```bash
# Clone the repo
git clone https://github.com/YOUR_USERNAME/tbb-website.git
cd tbb-website

# Start dev server (live reload at http://localhost:1313)
hugo server -D
```

## Editing Content

All content lives in the `content/` directory as Markdown files.

| Section         | Directory                  |
|-----------------|----------------------------|
| Home            | `content/_index.md`        |
| Registration    | `content/registration.md`  |
| Details         | `content/details/`         |
| Study Resources | `content/study-resources/` |
| Scholarships    | `content/scholarships/`    |
| About           | `content/about/`           |
| Photos          | `content/photos/`          |
| Donate          | `content/donate.md`        |
| Contact         | `content/contact.md`       |

### Front matter fields

Each `.md` file starts with a YAML block:

```yaml
---
title: "Page Title"
description: "One-line description for SEO."
weight: 1        # controls sidebar/card order within a section
---
```

### Adding a new page to an existing section

1. Create a new `.md` file in the appropriate `content/` subdirectory
2. Add front matter at the top
3. Write content in Markdown below

### Updating site-wide settings

Edit `hugo.toml` to update:
- Event dates, theme, scripture (`[params]` section)
- Social media links (`[params]` section)
- Navigation menu (`[[menus.main]]` entries)

## Deployment

Pushing to `main` automatically builds and deploys via GitHub Actions.

### One-time GitHub setup

1. Create a new GitHub repository
2. Push this code: `git remote add origin https://github.com/YOUR_USERNAME/tbb-website.git && git push -u origin main`
3. In the repo → **Settings → Pages → Source → GitHub Actions**
4. The first push triggers a build; the site will be live at `https://YOUR_USERNAME.github.io/tbb-website/`

### Custom domain (texasbiblebowl.org)

1. `static/CNAME` already contains `texasbiblebowl.org`
2. At your domain registrar, add a CNAME record: `www` → `YOUR_USERNAME.github.io`
3. For the apex domain, add A records pointing to GitHub Pages IPs:
   ```
   185.199.108.153
   185.199.109.153
   185.199.110.153
   185.199.111.153
   ```
4. In GitHub repo → Settings → Pages → Custom domain → enter `texasbiblebowl.org`

## Contact Form

The contact form uses [Formspree](https://formspree.io) (free: 50 submissions/month):

1. Sign up at formspree.io
2. Create a new form
3. Replace `YOUR_FORM_ID` in `content/contact.md` with your form ID

## Things Still Needed

These two items require information only you can provide:

- [ ] **Formspree form ID** — Sign up at formspree.io, create a form, and replace `YOUR_FORM_ID` in `content/contact.md`
- [ ] **Amazon Wish List URL** — Replace the `#` placeholder in `content/donate.md` with the real Amazon registry URL
- [ ] **2023 photos** — The 2023 photo gallery (`content/photos/2023.md`) currently shows a placeholder message; add photos if available

Everything else has been completed:
- [x] All PDF download links filled in (`content/study-resources/downloads.md`)
- [x] All Kahoot and practice test links filled in (`content/study-resources/practice-tests.md`)
- [x] News article URLs filled in (`content/about/in-the-news.md`)
- [x] Google Maps embeds added to `content/about/locations.md`
- [x] Photo galleries built for 2025 (1,011 photos) and 2024 (10 photos)
- [x] YouTube URL added to `hugo.toml`
- [x] TBB logo and favicon added; logo shown in navbar and footer
- [x] Hero image ("GOD'S PROMISES UNBROKEN") on homepage
- [x] All PDFs (20 MB) and web-resolution photos (40 MB) copied to `static/uploads/`
