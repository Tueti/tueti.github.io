# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is a Hugo static site blog at [tueti.space](https://tueti.space) — a German-language personal blog about tech, photography, and other topics. The PaperMod theme is included as a git submodule.

## Common Commands

> ⚠️ Hugo muss lokal installiert sein — auf manchen Systemen (z.B. NAS) ggf. nicht verfügbar. Build läuft via CI/CD (`pages.yml`).

```bash
# Start local development server with live reload
hugo server

# Build for production (minified)
hugo --minify

# Create a new post
hugo new content posts/<slug>/index.md
```

## Deployment

Deployment is handled automatically on push to `main` via **`pages.yml`** — builds and deploys to GitHub Pages.

## Content Structure

Posts live in `content/posts/<slug>/` as page bundles — each post is a directory containing `index.md` and any associated images (`.webp` preferred):

```
content/posts/my-post-slug/
  index.md
  featured-image.webp
  other-image.webp
```

Post front matter example:
```yaml
---
author: ["Chrischi"]
title: "Post Title"
slug: "url-slug"
date: "2025-01-01"
draft: false
description: "Short description for meta tags"
summary: "Longer summary shown in post lists"
tags: ["Tag1", "Tag2"]
categories: ["Category"]
series: ["Series Name"]
cover:
  image: featured-image.webp
  caption: "Caption text"
---
```

## Custom Layouts & Extensions

The site overrides/extends PaperMod with:

- **`layouts/custom_page.html`** — Custom page type for static pages (Impressum, Über mich)
- **`layouts/_default/indexed_archive.html`** — Custom indexed archive page
- **`layouts/_default/rss.xml`** — Customized RSS feed template
- **`layouts/partials/footer.html`** — Custom footer
- **`layouts/partials/extend_head.html`** — Head tag extensions
- **`layouts/shortcodes/image_with_caption_and_link.html`** — Custom shortcode for images with optional link and caption

To use the image shortcode in posts:
```
{{< image_with_caption_and_link src="image.webp" alt="Alt text" caption="Caption" link="https://example.com" >}}
```

- **`assets/css/extended/custom.css`** — Custom CSS extending PaperMod styles

## Custom Commands

- **`/review-post`** — Führt ein strukturiertes Lektorat & SEO-Review eines Posts durch (`.claude/commands/review-post.md`)

## Theme

PaperMod is installed as a git submodule in `themes/PaperMod/`. Do not edit theme files directly — use the override/extension mechanisms above instead. When cloning, initialize submodules with `git clone --recurse-submodules` or run `git submodule update --init` afterwards.
