# Brickwatts portfolio site — design

Date: 2026-11-05
Status: approved (pending written-spec review)
Figma: https://www.figma.com/design/Xa3blWKOESGnLffTaCuibV/Brickwatts

## Goal

Replace the current single holding page with a multi-page portfolio site for Brickwatts, a construction company serving East Sussex, Kent and West Sussex. The site shows completed work, introduces Justin Watts and the team, explains subcontracting for other builders, presents client testimonials, and makes contacting Justin easy.

Success criteria:

- WCAG 2.2 AA across every page, in both light and dark themes.
- Light and dark themes, following the system setting by default, with a persistent manual toggle.
- New projects and testimonials can be added by a developer by adding one Markdown file or one JSON entry.
- Stays on the current stack and hosting (Eleventy 3, Tailwind 4, GitHub Pages).

## Decisions

| Topic | Decision |
|---|---|
| Content editing | Developer-managed: Markdown and JSON in the repo. No CMS. Content model is CMS-ready (front matter) should one be added later. |
| Structure | Multi-page, with one page per project. |
| Video | Short muted loops are self-hosted MP4 (under about 15 MB each). Longer videos are YouTube embeds behind a click-to-load facade. |
| Contact | Direct links only: phone, email, WhatsApp. No form. |
| Visual identity | Evolve: keep the logo, green and gold, and the brick-corner motif. The design is photo-led, with brand colour as a frame. |
| Testimonials | Dedicated page, plus pulled quotes on Home and on the matching project page. |
| Media at launch | Placeholders for all images and video. Real assets are swapped in later. |
| Fonts | Archivo for headings and Inter for body, both self-hosted. |

## Information architecture

```
/                    Home
/about/              About: Justin and the team
/work/               Our Work: project index
/work/<slug>/        Project detail
/subcontracting/     Working for other builders
/testimonials/       All testimonials
/contact/            Contact
```

### Global

- **Header**:
  - logo linking home;
  - primary nav (Home, About, Our Work, Subcontracting, Testimonials, Contact);
  - theme toggle;
  - "Call Justin" `tel:` button.
- **Mobile header**: the nav collapses to a disclosure button with `aria-expanded`, and the call button becomes an icon button with an accessible name.
- **Footer**:
  - phone, email, WhatsApp;
  - coverage area;
  - the services list;
  - company details line;
  - copyright.
- **Page structure on every page**:
  - "Skip to content" link;
  - `header`, `nav`, `main` and `footer` landmarks;
  - exactly one `h1`.

### Home `/`

1. **Hero**
   - Full-bleed image, or a short muted looping video with a visible pause button.
   - `h1` headline, the coverage-area line, and two calls to action: "Call Justin" (`tel:`) and "See our work" (`/work/`).
   - The brick-corner line motif frames the hero.
2. **Services strip**: brickwork, extensions, fireplaces and chimneys, groundworks, landscaping and patios, lime and stone, drainage, new builds. Each item links to `/work/?service=<slug>`.
3. **Featured work**: three to six project cards where `featured: true`, then a "View all work" link.
4. **Testimonials**: two or three quotes, then a link to `/testimonials/`.
5. **Subcontracting teaser**: "Builders: need a reliable team?" linking to `/subcontracting/`.
6. **Contact band**: phone, email and WhatsApp.

### About `/about/`

- Justin's story: intro text plus a portrait placeholder.
- Team grid from `team.json`. Each member has a photo, name, role and a short bio.
- "How a job runs": four steps (visit, quote, build, sign-off).
- Coverage area: a list of counties and towns. A map is optional and out of scope for v1.
- Accreditations and insurance block, shown only if data exists.

### Our Work `/work/`

- `h1` plus a short intro.
- **Service filter chips**:
  - Built as progressive enhancement. Without JS, all projects show and the chips are links to `?service=`.
  - With JS, filtering happens in place.
  - The active state uses `aria-pressed`, and a live region announces the result count.
- **Project cards**: cover image, title, town, service tags. The whole card is one link.

### Project `/work/<slug>/`

- `h1` title, then a meta line: location, services, year.
- Short description (Markdown body).
- **Gallery**:
  - responsive grid of thumbnails;
  - the lightbox opens on activation;
  - the lightbox is a `<dialog>` with a focus trap, Esc to close, arrow keys for previous/next, a visible close button, and focus returned to the trigger on close.
- Optional before/after pair, shown as two labelled images side by side. No slider in v1.
- **Videos**:
  - Self-hosted: `<video controls muted playsinline>` with a poster, and captions (`<track>`) required when there is speech.
  - YouTube: a facade `<button>` labelled "Play video: <title>" that injects a `youtube-nocookie.com` iframe on click.
- Client testimonial for this project, if one exists.
- Previous and next project links.

### Subcontracting `/subcontracting/`

Copy is placeholder until Justin confirms it.

- Who it's for: main contractors and builders.
- What's offered: trades list, labour-only or supply-and-fix.
- How it works: enquiry, then site visit or drawings, then price (day rate or fixed), then schedule, then works, then sign-off.
- Insurance and compliance: public liability and CSCS. Placeholder.
- Example jobs done for other builders: project cards filtered by the `subcontract: true` flag.
- Contact call to action.

### Testimonials `/testimonials/`

All testimonials, always sorted newest first by `date`.

- **Job-type filter chips**, which reuse the Our Work filter component and behaviour:
  - "All" is the default and is active on load.
  - The other chips are the services list plus "Subcontracting".
  - Progressive enhancement: without JS, all testimonials show and each chip links to `?service=`.
  - With JS, filtering happens in place, and a polite live region announces "Showing N testimonials · newest first".
  - A testimonial matches a chip through its `service` field. Testimonials with no `service` appear only under "All".

Each testimonial shows:

- quote, name, town, service;
- a link to the project if `project` is set;
- the source name and link if `source` is set.

### Contact `/contact/`

- Large `tel:`, `mailto:` and WhatsApp (`https://wa.me/44…`) links.
- Coverage area and typical response time.
- "What to send us": photos, postcode, rough scope, timescale.

## Content model

### `src/_data/site.json`

```json
{
  "name": "Brickwatts",
  "domain": "brickwatts.co.uk",
  "phone": "07816351074",
  "phoneIntl": "+447816351074",
  "email": "brickwattsltd@outlook.com",
  "whatsapp": "447816351074",
  "areas": ["East Sussex", "Kent", "West Sussex"],
  "services": [{ "slug": "brickwork", "name": "Brickwork" }]
}
```

### `src/work/<slug>.md`

```yaml
---
title: Victorian fireplace restoration
location: Rye, East Sussex
year: 2025
services: [fireplaces, lime-stone]
featured: true
subcontract: false
cover: { src: /assets/img/placeholder/landscape.svg, alt: "Restored brick fireplace with oak beam" }
images:
  - { src: ..., alt: "..." }
before: { src: ..., alt: "..." }   # optional
after:  { src: ..., alt: "..." }   # optional
videos:
  - { type: file, src: ..., poster: ..., captions: ..., title: "..." }
  - { type: youtube, id: "...", title: "..." }
---
Short description in Markdown.
```

### `src/_data/testimonials.json`

```json
[{ "quote": "...", "name": "...", "town": "...", "service": "extensions",
   "project": "victorian-fireplace", "source": { "name": "Google", "url": "..." },
   "date": "2025-06-01" }]
```

- `date` is required, because sorting depends on it.
- `service` is a service slug or `subcontracting`, and drives the job-type filter.
- `project` and `source` are optional.

### `src/_data/team.json`

```json
[{ "name": "Justin Watts", "role": "Founder", "bio": "...",
   "photo": { "src": "...", "alt": "..." } }]
```

## Visual system

### Direction

The design is photo-led: project imagery carries the pages, and brand green and gold frame it. The brick-corner line motif, from the current site, frames the hero and featured imagery. It draws in once, and not at all under `prefers-reduced-motion: reduce`.

### Colour tokens

These are CSS custom properties mapped into the Tailwind 4 `@theme`. The values are a starting point. Every text and UI pair must be verified with a contrast checker before the values are final.

| Token | Dark | Light | Use |
|---|---|---|---|
| `--bg` | `#1c2217` | `#f6f3ea` | Page background |
| `--surface` | `#2e3528` | `#ffffff` | Cards, bands |
| `--text` | `#f3efe2` | `#1f2619` | Body text |
| `--muted` | `#c9c4b0` | `#4a5240` | Secondary text |
| `--accent` | `#c8a94c` | `#7d6220` | Links, focus ring, underline, button fill |
| `--accent-hi` | `#fcec97` | `#9a7a26` | Display gradient, decoration (large text only in light) |
| `--on-accent` | `#1c2217` | `#ffffff` | Text on accent buttons and chips |
| `--on-brand` / `--on-brand-accent` | `#f3efe2` / `#fcec97` | same | Text on green bands, the same in both themes |
| `--placeholder` / `--placeholder-ink` | `#3a4232` / `#8d927f` | `#e4dfd1` / `#6b6f5f` | Media placeholders |

Contrast was checked on 2026-11-05. The original light accent, `#8a6d22`, measured 4.41:1 on `--bg`, which fails AA, so it changed to `#7d6220`.

| Pair | Ratio |
|---|---|
| Light `--accent` on `--bg` | 5.20:1 |
| Light `--accent` on `--surface` | 5.77:1 |
| White on light `--accent` | 5.77:1 |
| Light `--accent-hi` on `--bg` | 3.65:1 (large text only) |
| Dark `--accent` on `--bg` | 7.16:1 |
| `--on-accent` on dark `--accent` | 7.16:1 |
| `--muted` on `--bg` (dark / light) | 9.3:1 / 7.4:1 |

Some areas stay dark in both themes, by locking the Dark variable mode in Figma and setting `data-theme="dark"` on the section in code:

- the hero (photo plus scrim);
- page-hero bands;
- the subcontracting teaser;
- CTA bands;
- the footer.
| `--brand` | `#2a381d` | `#2a381d` | Green band (the same in both themes) |
| `--border` | `#3d4535` | `#ddd6c4` | Card borders, dividers |

Rules:

- Body text against its background is at least 4.5:1. Large text and UI components are at least 3:1.
- The gold gradient appears only on display text of 32px or larger, and only over `--bg`, `--surface` or `--brand`. It has a solid `--accent-hi` fallback.
- Gold is never the only signal of state.

### Typography

| Role | Font | Notes |
|---|---|---|
| Headings | Archivo (variable, 600–800) | Self-hosted WOFF2 via `@fontsource-variable/archivo`. Figma uses normal width, because semi-condensed is not available there. Code may use `font-stretch: 87.5%` if Tom prefers that look. |
| Body / UI | Inter (variable, 400–600) | Self-hosted via `@fontsource-variable/inter` |

- The fluid scale uses `clamp()` for `h1` to `h4` and for body text.
- Body text is 17–18px with line-height 1.6 and a measure of 65ch at most.

### Components

- Button: primary (gold background, dark text) and secondary (outline).
- Nav link and theme toggle.
- Service chip, as a link or a toggle button.
- Project card.
- Testimonial quote, as a `figure`/`blockquote`/`figcaption`.
- Section heading with a gold rule.
- Gallery thumbnail.
- Lightbox.
- Video facade.
- Contact link block.
- Corner-motif frame.

Shape and states:

- Radius is 4px. Cards have a 1px `--border` and no shadows.
- Hover and focus show a gold underline or border.
- The focus ring is 3px `--accent` with a 2px offset, on every interactive element.
- Touch targets are at least 44×44px.

### Themes

- Default: follow `prefers-color-scheme`.
- The header toggle sets `data-theme="light|dark"` on `<html>` and saves the choice to `localStorage`. Storage access is wrapped in try/catch.
- A tiny inline script in `<head>` applies the stored theme before first paint, so the wrong theme never flashes.
- The toggle is a `<button>` with `aria-pressed` and a label that states the current mode.

### Motion

- The only motion is the corner draw-in, hover transitions (150ms), and lightbox fade.
- All of it is disabled under `prefers-reduced-motion`.
- The hero video does not autoplay under reduced motion, and always has a pause button.

## Accessibility requirements (WCAG 2.2 AA)

- Semantic landmarks, one `h1` per page, and a logical heading order.
- A skip link, visible on focus.
- Every image has `alt`. The build fails if any `cover`, `images[]`, `before`, `after` or team photo has no `alt`. Decorative images use `alt=""` explicitly.
- Keyboard: every interactive element is reachable and operable, with no keyboard traps except the modal lightbox, which releases on Esc or close.
- Focus is always visible and never hidden behind the sticky header (`scroll-padding-top`).
- Colour contrast is as in the Visual system section, verified in both themes.
- Captions are required for self-hosted video with speech. The YouTube facade has an accessible name.
- Filter results are announced through a polite live region.
- The language is `lang="en-GB"`.
- Pages still work at 200% zoom, and reflow at 320px width with no horizontal scroll.

## Build and architecture

- **Stack**: Eleventy 3, with Tailwind 4 compiled by the existing PostCSS `eleventy.before` hook. Deployed by GitHub Pages through `.github/workflows/static.yml`.
- **Remove Alpine.js**: the current `alpinejs@3.x.x` is unpinned. Replace it with small vanilla ES modules in `src/assets/js/`:
  - `theme.js`
  - `nav.js`
  - `filter.js`
  - `lightbox.js`
  - `video-facade.js`
- **Images**: `@11ty/eleventy-img` shortcode outputs `<picture>` with AVIF, WebP and a fallback, a `srcset`, `width`/`height` and `loading="lazy"`. The hero image is eager and `fetchpriority="high"`.
- **Layouts and partials**: in `src/_includes/`:
  - `layouts/base.njk`
  - `layouts/page.njk`
  - `layouts/project.njk`
  - partials: `header`, `footer`, `project-card`, `quote`, `gallery`, `video`, `contact-links`, `corner-frame`.
- **Collections**:
  - `work`, built from `src/work/*.md` and sorted by `year`, newest first;
  - `featuredWork`;
  - `subcontractWork`.
- **Placeholders**: neutral SVG placeholders in `src/assets/img/placeholder/` (landscape, portrait, square, video poster), each labelled with its role and size. One short placeholder MP4 loop is optional. If it is skipped, the hero uses an image.
- **SEO**:
  - per-page `title` and `description`;
  - Open Graph and Twitter tags;
  - canonical URLs;
  - `sitemap.xml` and `robots.txt`;
  - `LocalBusiness` (`GeneralContractor`) JSON-LD with `areaServed`, `telephone` and `email`.
- **Fix**: `<meta name="viewport" content="width=device-width, initial-scale=1">`. The current value has the typo `width=inital`.

## Testing

- `npm test`:
  1. Builds the site.
  2. Serves `dist/`.
  3. Runs Playwright with `@axe-core/playwright`.
- **Playwright and axe coverage**:
  - Every page has zero axe violations, in light and in dark theme.
  - Keyboard: skip link works; the nav disclosure opens and closes; the lightbox opens, traps focus, cycles with the arrow keys, closes on Esc, and returns focus.
  - The theme toggle persists across navigation and reload, and does not flash the wrong theme.
  - Filter, on Our Work and Testimonials: the chips filter the items and update the live region, and "All" is the default. With JS disabled, all items show.
  - Testimonials render in descending `date` order.
  - Reduced motion: no corner animation and no hero autoplay.
  - The YouTube facade loads the iframe only after a click.
- **Build-time check**: the build fails on missing image `alt`.
- **CI**: the GitHub Actions build job runs `npm test` before upload and deploy.

## Delivery sequence

1. This spec, reviewed by Tom.
2. **Figma** (file `Xa3blWKOESGnLffTaCuibV`):
   - variables collection with light and dark modes;
   - text styles;
   - core components;
   - pages at desktop 1440 and mobile 390;
   - Home and Project in both themes, the other pages in dark.
3. Tom reviews the Figma.
4. Implementation plan (writing-plans), then the build on the `redesign` branch.

## Out of scope (v1)

- CMS.
- Contact form.
- Map embed.
- Before/after slider.
- Analytics. If added later, it needs a privacy notice.
- Blog or news.
