# Repository Guidelines

## Project Structure & Module Organization

This repository contains the German-language Hugo site for `tueti.space`. Site configuration lives in `hugo.yaml`. Articles are leaf bundles under `content/posts/<slug>/`; keep each post's `index.md`, `featured-image.webp`, and other local images together. Standalone pages and taxonomy definitions also live in `content/`. Site-specific templates belong in `layouts/`, custom styling in `assets/css/extended/custom.css`, and files copied unchanged to the published site in `static/`.

`themes/PaperMod/` is a Git submodule. Do not edit it directly; override templates in the root `layouts/` tree or extend styles under `assets/`. Generated output in `public/` is not source and should not be committed.

## Build, Test, and Development Commands

- `git submodule update --init --recursive` installs the PaperMod theme after cloning.
- `hugo server -D` starts a live-reloading local server and includes draft posts.
- `hugo --gc --minify` performs the production build used for validation.
- `hugo new content posts/<slug>/index.md` creates a draft from `archetypes/default.md`.

Use Hugo Extended; CI currently builds with version `0.164.0`. A push to `main` triggers `.github/workflows/pages.yml` and deploys to GitHub Pages.

## Content, Style & Naming Conventions

Use lowercase, hyphen-separated directory names and URL slugs, for example `content/posts/mein-neuer-beitrag/`. Write posts in German and follow the YAML front matter patterns in neighboring articles: include a title, date, draft state, description or summary, and relevant tags. Prefer WebP for article images, descriptive lowercase filenames, meaningful alt text, and relative paths within the page bundle.

Match existing formatting in YAML, HTML, CSS, and Markdown: two-space YAML indentation, readable template blocks, and focused overrides. Avoid reformatting unrelated files.

## Testing Guidelines

There is no dedicated automated test suite or coverage target. Before submitting, run `hugo --gc --minify`; warnings, broken shortcodes, or build failures must be resolved. Also preview changed pages with `hugo server -D` and check navigation, responsive layout, images, links, code blocks, and both light and dark themes when styling changes.

## Commit & Pull Request Guidelines

History uses short, imperative, change-focused subjects, often in German (for example, `typos fix` or `Neuer Beitrag`). Keep each commit scoped to one content or site change. Pull requests should explain what changed, identify affected pages, confirm a successful Hugo build, and include before/after screenshots for visual changes. Link an issue when one exists and call out draft-state, permalink, configuration, or submodule changes explicitly.
