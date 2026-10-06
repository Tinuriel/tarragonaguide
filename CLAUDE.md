# Tarragona guide — how this site works

Multilingual travel portal about Tarragona and Catalonia, built with **Hugo**
(installed via npm: `npm install`, then `npm run dev` / `npm run build`) and
deployed to **Cloudflare Workers** (`wrangler.jsonc`: Workers Builds runs the
build command and uploads `./public` on every push to the production branch).

## Languages

| code | label | role |
|------|-------|------|
| `ru` | RU | source language (default, `/ru/`) |
| `uk` | UA | source language (the owner also writes in Ukrainian) |
| `en` | EN | translated |
| `es` | ES | translated |

Every page exists in all four languages. URLs: `/<lang>/<section>/<slug>/`.
Slugs are English, identical in every language (they are the folder names).
The site root `/` redirects to the visitor's saved/browser language.

## Content layout

```
content/
  _index.<lang>.md            home page intro
  <section>/_index.<lang>.md  section title (+ optional intro text)
  <section>/<slug>/           one post = one folder ("page bundle")
    index.ru.md index.uk.md index.en.md index.es.md
    photo-1.jpg …             photos shared by all 4 languages
```

Sections: `sights`, `beaches`, `food`, `catalonia`, `plan`.
Files in the same folder are automatically linked as translations of each other.

## Post front matter

```yaml
---
title: "Кафедральный собор"
description: "One sentence shown on cards and in search results."
weight: 40                 # order inside the section (same in all languages)
cover: cathedral-2.jpg     # optional, photo from the folder
place_id: ChIJ...          # optional, Google Maps place id → "Open in Google Maps"
emoji: ⛪                  # optional, card placeholder when there is no photo
translated_from: ru        # ONLY on translated files
reviewed: false            # ONLY on translated files; true after a human proofread
---
```

Non-content fields (`weight`, `cover`, `place_id`, `emoji`) must be identical in
all four files of a post.

## Shortcodes (use instead of raw HTML)

- `{{< place "PLACE_ID" >}}` — "📍 Open in Google Maps" line, localized.
- `{{< map "PLACE_ID" >}}` — inline link with text "Google Maps", localized:
  `**Olo Café** ({{< map "ChIJ..." >}})`.
- `{{< tip >}}💡 Markdown text{{< /tip >}}` — highlighted tip box.
- `{{< gallery >}}` one `file.jpg | caption` per line `{{< /gallery >}}`.
- Links to other posts: `[text]({{< relref "/beaches/el-miracle" >}})` — resolves
  to the same language automatically.

## Translation workflow

When the owner provides new text (Russian or Ukrainian):
1. Create/update the file in that language without `translated_from`.
2. Write the other three languages with `translated_from: <source>` and
   `reviewed: false`. Translate naturally (not word for word), keep the owner's
   personal first-person voice, keep proper names of places/dishes as they are
   locally used (Catalan/Spanish), keep emoji, shortcodes and place ids unchanged.
3. When a source file changes, update the three translations and reset
   `reviewed: false` on them.
4. Run `npx hugo --gc` and make sure it builds with no errors or warnings.

`_legacy/index.html` is the old single-page site (RU/EN/UK blocks) kept for reference.
