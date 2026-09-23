# jeonseungwoo1.github.io

Personal site of Jeon Seungwoo (전승우), AirLab at Dongguk University. It covers robot manipulation, world models, and VLAs.
It builds with GitHub Pages' built-in Jekyll, with no external theme or plugins.

## Layout

| Path | What it holds |
|---|---|
| `_config.yml` | Name, affiliation, links (empty values are hidden), `research` items, and `projects` cards (the section is hidden while `projects` is empty) |
| `_posts/` | Weekly briefs (`YYYY-MM-DD-weekly-world-model-brief.md`) |
| `weekly.html` | Weekly brief archive (`/weekly/`) |
| `site.css` | All styles (light and dark follow the system setting) |

## Common edits

- **Add a project:** add an entry under `projects:` in `_config.yml`, using the format in the comments just above it. Put thumbnails in an `images/` folder.
- **Add a CV:** upload the PDF as `cv.pdf`, then set `cv: /cv.pdf` in `_config.yml`.
- **Weekly post front matter:** `layout: post`, `title`, `date`, `papers` (number of papers; do not name it `count`), `summary`, and `tags`. The `date` must not be in the future, or Jekyll skips the post.

## Weekly auto-publishing

Every Friday at 19:00 KST, a scheduled Claude task reads that week's papers from a private briefing dashboard. It picks the top 10 and commits them to `_posts/` as a new post written in Korean.
Personal notes, stars, and read state never go into public posts.
