# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

HUB导航 (For.Pub) — a static, single-page website navigation directory for developers. Hosted via GitHub Pages at `for.pub`.

## Architecture

Single-file application (`index.html`) with a separate data file (`forpub.json`):

- `index.html` — all HTML, CSS, and JS in one file. No build tools, no frameworks, no dependencies.
- `forpub.json` — array of category groups, each containing link items. This is the primary data source.
- `icon/` — SVG/PNG icons referenced by items (116 files). Filenames must match the `icon` field in JSON entries.

Data flow: page loads with hardcoded "热门推荐" (hot picks) data for instant render, then async-fetches `forpub.json` and merges via `mergeData()`.

## Data Format

```json
[
  {
    "category": "分类名称",
    "items": [
      {
        "title": "站点名",
        "link": "https://example.com",
        "icon": "./icon/example.svg",
        "tags": ["标签1", "标签2"],
        "desc": "一句话描述"
      }
    ]
  }
]
```

Items with duplicate `link` values are deduplicated during render. The `link` field must be a valid URL.

## Adding/Updating Navigation Entries

1. Edit `forpub.json` — add or modify entries in the appropriate category
2. Place the icon file in `icon/` — SVG preferred, PNG acceptable
3. The `icon` path in JSON must be `./icon/<filename>`
4. Category names in `forpub.json` must not collide with the hardcoded "热门推荐" category (that one lives in `index.html`)

To modify "热门推荐" items, edit the `data` array directly in `index.html` (around line 89).

## Development

No build step. To preview locally:

```bash
python3 -m http.server 8080
```

Deployed automatically via GitHub Pages on push to `main`.

## Code Notes

- XSS protection: `escapeHTML()` is used on all rendered text
- Search filters on title, desc, link, and tags (case-insensitive, 300ms debounce)
- Tab state is persisted via URL hash (`#分类名`)
- CSS uses `color-mix()` for tag backgrounds from the primary color (`--primary`)
