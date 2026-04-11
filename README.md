# froskr-website

Source for [froskr.com](https://froskr.com) — the Froskr marketing site.

Static HTML + CSS + JS, served by GitHub Pages from the `main` branch. No build step, no framework, no dependencies. Edit the HTML files directly and push; GitHub Pages rebuilds in ~30–60 seconds.

## Repo layout

```
froskr-website/
├── index.html                                     Home page (froskr.com)
├── legal/index.html                               Legal / compliance page (/legal)
├── how-we-work-with-providers/index.html          MSP partner page (/how-we-work-with-providers)
├── blog/
│   ├── index.html                                 Blog index (/blog)
│   ├── aba-rule-1-6-cybersecurity-reasonable-efforts/index.html
│   └── microsoft-365-security-checklist-law-firms/index.html
├── favicon.svg                                    Site favicon (root, per web convention)
├── og-image.png                                   Open Graph preview image (1200×630)
├── sitemap.xml                                    Sitemap for search engines
├── robots.txt                                     Robots directives
├── CNAME                                          Custom domain config for GitHub Pages
└── README.md                                      This file
```

## Related repo

The M365 security assessment tool at [assess.froskr.com](https://assess.froskr.com) lives in a separate repo: [`starforge-assessment`](https://github.com/star-forge-advisors/starforge-assessment). The "Run Free Assessment" and "Run the free M365 assessment" CTAs on this site link to it.

## Editing

Each HTML file is self-contained. All CSS is inline inside a single `<style>` block near the top of each file; all JS is inline at the bottom. To edit a page, just open the file and change the HTML/CSS/JS in place.

### Theme

The site uses a shared "Theme A" palette defined at the top of every `<style>` block as CSS custom properties on `:root`:

```css
:root {
  --midnight: #050506;   /* page background          */
  --deep:     #0A0B0F;   /* card / panel background  */
  --navy:     #0F1116;
  --slate:    #16181F;
  --blue:     #00E5FF;   /* primary accent (cyan)    */
  --white:    #F5F7FA;   /* body text                */
  /* ...plus greyscale ramp --g1..--g6 */
}
```

The typeface is [Inter](https://fonts.google.com/specimen/Inter), loaded from Google Fonts at the top of every page.

**To change a theme color site-wide:** update the `:root` block in every HTML file. There are 6 pages. This is known tech debt — see "Known tech debt" below.

### Navigation

Every page has the same sticky top nav with:
- Frog logo + FROSKR wordmark (inline SVG, linked to `/`)
- Links to the home-page sections, `/blog`, and `/legal`
- A solid "Run Free Assessment" button → `https://assess.froskr.com`
- An outlined "Contact us" button → `#contact` on the home page

The nav markup is duplicated across all 6 files — also known tech debt.

### Forms

The contact form on the home page posts to [Formspree](https://formspree.io) at `https://formspree.io/f/xdawrnnk`. Submissions arrive at the address configured in the Formspree dashboard (hello@froskr.com).

### Scheduler

"Book a 30-min call" links point to `https://calendly.com/vishak-froskr/30min`. If that URL changes, it lives in:
- `index.html` (home page — 2 occurrences: hero inline link and contact form card)
- The assessment repo at `starforge-assessment/index.html` (1 occurrence)

## Deployment

GitHub Pages serves the `main` branch. Pushing to `main` triggers a rebuild automatically. Custom domain `froskr.com` is configured via the `CNAME` file and DNS.

## Local preview

Any static file server works. From the repo root:

```bash
python3 -m http.server 8000
# then open http://localhost:8000
```

Or use `npx serve .` if you prefer Node.

## Known tech debt

A dev taking this over should know the following trade-offs were deliberate (fast iteration, zero build tooling) but cost maintainability:

1. **Inline CSS and JS.** Every HTML file has its own `<style>` and `<script>` block. The theme palette, nav styles, footer styles, and frog logo SVG are duplicated across 6 files. A single color change requires editing 6 places. Biggest cleanup win: extract shared styles into `/assets/css/main.css` and reference from every page.

2. **Duplicated nav and footer markup.** Same story — every page has a copy. A static-site generator (Eleventy, Astro, 11ty, or even plain Jekyll which GitHub Pages supports natively) would let you template these once. Not needed today, worth it if the site grows past ~10 pages.

3. **No link checker in CI.** External links (Calendly, Formspree, assess.froskr.com) can break silently. A weekly GitHub Action running `lychee` or similar would catch rot.

4. **Open Graph image is a PNG at the root.** Convention is fine; if you later move assets into `/assets/`, remember to update the `og:image` meta tag in all 6 HTML files AND re-scrape in the LinkedIn/Twitter post inspectors to refresh their caches.

5. **Inline structured data (JSON-LD).** Each page has hand-written JSON-LD in the `<head>`. Any change to business info (address, phone, logo URL) needs to be made in every page.

## Quick reference: where things live

| What | File(s) |
| --- | --- |
| Home page copy | `index.html` |
| Contact form target | `index.html` → `<form action="https://formspree.io/f/xdawrnnk">` |
| Calendly URL | `index.html` (2×), assess repo `index.html` (1×) |
| Legal copy (privacy, terms, DPA) | `legal/index.html` |
| Blog posts | `blog/*/index.html` |
| MSP partner page | `how-we-work-with-providers/index.html` |
| Theme colors | `:root {}` block in every HTML file |
| Favicon | `favicon.svg` |
| Social preview image | `og-image.png` |
| Custom domain | `CNAME` |
| Search index | `sitemap.xml`, `robots.txt` |
