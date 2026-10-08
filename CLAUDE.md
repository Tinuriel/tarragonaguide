# Tarragona guide — how this site works

Multilingual travel portal about Tarragona and Catalonia, built with **Hugo**
(installed via npm: `npm install`, then `npm run dev` / `npm run build`) and
deployed by `.github/workflows/pages.yml` to **GitHub Pages** on every push to
`main` (Pages source must be "GitHub Actions"). `wrangler.jsonc` also supports
Cloudflare Workers Builds. Never rely on the raw repo being served: the site only
exists after the Hugo build.

## Languages

| code | label | role |
|------|-------|------|
| `ru` | RU | source language (default, `/ru/`) |
| `uk` | UA | source language (the owner also writes in Ukrainian) |
| `en` | EN | translated |
| `es` | ES | translated |
| `ca` | CA | translated (Catalan) |

Every page exists in all five languages. URLs: `/<lang>/<section>/<slug>/`.
Slugs are English, identical in every language (they are the folder names).
The site root `/` redirects to the visitor's saved/browser language.

## Content layout

```
content/
  _index.<lang>.md            home page intro
  <section>/_index.<lang>.md  section title (+ optional intro text)
  <section>/<slug>/           one post = one folder ("page bundle")
    index.ru.md index.uk.md index.en.md index.es.md index.ca.md
    photo-1.jpg …             photos shared by all 5 languages
```

Sections: `sights`, `beaches`, `food`, `catalonia`, `plan`, `cities` (other cities,
one sub-folder per city, e.g. `cities/barcelona/`).

Every city gets the same standard sub-sections (each with `_index.<lang>.md` in all
languages, titles and emoji same as Tarragona's sections): `sights` (weight 10),
`beaches` (20), `food` (30), `shopping` (40), `plan` (50, general tips). Posts go inside
them: `cities/barcelona/food/<slug>/`. A sub-section with no posts is hidden from the
lists automatically (and marked noindex), so create all five when adding a new city —
use `cities/barcelona/*/_index.*.md` as the template. When a post moves to another
folder, add `aliases: ["/<old path>/"]` so the old URL redirects.
`blog` is a special section with no posts of its own: `/blog/` lists the posts of
any section that have `blog: true`, newest first (by `date`), with a filter by
section (shown once 2+ sections have blog posts); the home page shows the 3 newest
at the top. New review/place posts from the owner go to the blog (`blog: true`);
older guide pages are not in the blog unless the owner asks.
Files in the same folder are automatically linked as translations of each other.

## Post front matter

```yaml
---
title: "Кафедральный собор"
description: "One sentence shown on cards and in search results."
date: 2026-10-07           # day the post was added; required for blog posts, shown on the page
blog: true                 # optional, list this post in the blog
weight: 40                 # order inside the section (same in all languages)
cover: cathedral-2.jpg     # optional, photo from the folder
place_id: ChIJ...          # optional, Google Maps place id or share link → "Open in Google Maps"
emoji: ⛪                  # optional, card placeholder when there is no photo
info:                      # optional, ONLY for a post about one establishment:
  address: "Carrer de Llull, 145, Barcelona"   # info box at the top of the post
  hours: "…"               # (translated per language); also price, instagram, website
translated_from: ru        # ONLY on translated files
reviewed: false            # ONLY on translated files; true after a human proofread
---
```

Non-content fields (`date`, `blog`, `weight`, `cover`, `place_id`, `emoji`, `info.address`,
`info.instagram`, `info.website`) must be identical in
all five files of a post.

## Shortcodes (use instead of raw HTML)

- `{{< place "PLACE_ID" >}}` — "📍 Open in Google Maps" line, localized.
- `{{< map "PLACE_ID" >}}` — inline link with text "Google Maps", localized:
  `**Olo Café** ({{< map "ChIJ..." >}})`.
- Both map shortcodes also accept a full Google Maps link instead of the place id
  (e.g. a `https://maps.app.goo.gl/…` share link) — it is used as is.
- `{{< tip >}}💡 Markdown text{{< /tip >}}` — highlighted tip box.
- `{{< gallery >}}` one `file.jpg | caption` per line `{{< /gallery >}}`.
- Links to other posts: `[text]({{< relref "/beaches/el-miracle" >}})` — resolves
  to the same language automatically.

## Translation workflow

When the owner provides new text (Russian or Ukrainian):
1. Create/update the file in that language without `translated_from`.
2. Write the other four languages with `translated_from: <source>` and
   `reviewed: false`. Translate naturally (not word for word), keep the owner's
   personal first-person voice, keep proper names of places/dishes as they are
   locally used (Catalan/Spanish), keep emoji, shortcodes and place ids unchanged.
3. When a source file changes, update the four translations and reset
   `reviewed: false` on them.
4. Run `npx hugo --gc` and make sure it builds with no errors or warnings.

`_legacy/index.html` is the old single-page site (RU/EN/UK blocks) kept for reference.
