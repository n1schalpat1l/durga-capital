# DuRGA Capital website

Static HTML and CSS. No build step, no dependencies. Deploy this folder as-is to
any static host.

Preview locally:

```sh
# from the project root (the folder that contains site/)
python3 -m http.server -d site 8000
# or from inside site/
python3 -m http.server 8000
# then open http://localhost:8000
```

## Files

| Path | What it is |
|---|---|
| `index.html` | Home |
| `projects/durga-uday.html` | DuRGA UDAY project page. Copy it for a new project: `projects/<slug>.html`. |
| `projects/durga-uday/images/` | That project's images: AVIF + JPEG, 640px and full width. |
| `approach.html`, `about.html`, `contact.html` | To come |
| `css/brand.css` | Verbatim copy of the brand pack's `web/brand.css`. **Don't edit.** Replace it wholesale when the brand pack updates. |
| `css/site.css` | All site styles, mobile first. |
| `assets/` | Mark and favicons, copied from the brand pack. |

## Brand rules a developer is likely to break

- **The u in DuRGA is always lowercase.** In copy, wrap the name as
  `<span class="brand-name" translate="no">DuRGA</span>`. `site.css` forces
  `text-transform: none` on `.brand-name` and `.durga-lockup`. Don't put the
  name inside an `.eyebrow`, because eyebrows are set in uppercase.
- **The logo markup is copied verbatim** from the brand pack's
  `web/logo-snippets.html`. CAPITAL is seven `<span>`s so that it justifies to
  the width of DuRGA. Don't replace them with `letter-spacing`.
- **Size the lockup with `--durga-logo-size`, never below 32px.** At 32px the
  lockup is 186px wide; the minimum is 180px. Below that, use the mark alone.
- **Gold is never text on ivory or white.** For warm text on light grounds, use
  `--durga-copper`.

## Placeholders to replace

Search the site for `[`. Current list:

`[PRICE FROM]` `[PHASE 1 POSSESSION]` `[PHASE 2 POSSESSION]` `[3 BHK PLAN AND AREA]` `[RERA REG. NO.]` `[MAHARERA QR CODE]`
`[SPECIFICATION: …]` `[NEARBY: …]` `[EMAIL]` `[REGISTERED OFFICE ADDRESS]`
`[LEGAL ENTITY NAME]` `[CIN]` `[DOMAIN]`

`mailto:[EMAIL]` links need the real address in the `href` as well as the link
text. Phone links are already live (`tel:+919049700500`, `tel:+919860912043`).

Original renders and plan PDFs live in `../source/`, outside the deployed folder.
Renders are captioned "Artist's impression" wherever they appear.

## Performance

The homepage budget is 200KB. It currently transfers about 165KB with gzip.
About 155KB of that is web fonts. Newsreader alone is 129KB, because its
variable file includes the optical-size axis.

- Don't use Newsreader *italic* on the homepage. It adds another 63KB.
- Avoid characters outside the Latin subset, such as `→` or `₹`, in text set
  in these fonts. They trigger a second, latin-ext font download. Arrows on the
  site are inline SVG for this reason.
- Images go in with `loading="lazy"`, `decoding="async"` and explicit `width`
  and `height`. The exception is an image that is visible on first load.
- Make AVIFs with ffmpeg (libsvtav1), not macOS `sips`. Above about 1000px,
  `sips` writes tiled AVIFs that Chrome never paints. The exact command is in
  `../CLAUDE.md`.
- To get further under budget, self-host subset WOFF2 files (both fonts are
  SIL OFL 1.1).

## Adding languages

English stays at the root. Hindi goes in `hi/` and the regional language in
`<code>/`, using the same filenames, for example `hi/index.html` and
`hi/projects/durga-uday.html`. On each translated page:

- Set `<html lang="hi">`. Point the CSS and asset paths up one level.
- Uncomment the `hreflang` block in `<head>`. Every language version lists
  every other one.
- Add the language links to the header nav. A slot is commented in place.
- Load a Devanagari face (for example Noto Sans Devanagari) and scope it with
  `:lang(hi)`. `site.css` already removes letter-spacing for `:lang(hi)`;
  extend that rule for the regional script.
- Keep the wordmark as is. DuRGA in Latin script, inside `translate="no"`.

## Accessibility checks to keep passing

- One `h1` per page, and no skipped heading levels.
- All text at 4.5:1 contrast or better. Use the brand greys as they are and
  don't lighten them.
- Every link and button shows a visible `:focus-visible` outline: copper on
  light grounds, gold on navy.
- The site works with JavaScript off. The homepage ships no JS at all.
