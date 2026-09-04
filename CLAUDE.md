# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Local development server
hugo server

# Production build (outputs to /public)
hugo --minify
```

Deployment is automatic on push to `main` — Cloudflare Pages builds markovian.net, and `.github/workflows/deploy.yml` builds the legacy GitHub Pages copy. No manual deploy step needed.

Note: `baseURL` drives every absolute URL Hugo emits (canonical tags, OpenGraph, RSS, sitemap, and the search index's permalinks). Both deployments build from this one config, so both emit markovian.net links.

## Architecture

This is a Hugo static site using the **hugo-theme-stack** theme (git submodule at `themes/hugo-theme-stack/`). The site is served by Cloudflare Pages at https://markovian.net/ (the canonical domain, set as `baseURL` in `config.yaml`). A legacy GitHub Pages deployment at https://saan-volta.github.io/ also builds from this repo.

### Configuration hierarchy

- `config.yaml` — site-level config (baseURL, math, favicon, sidebar). Site values override theme defaults.
- `themes/hugo-theme-stack/config.yaml` — theme defaults (comments, widgets, date formats).

### Content

All posts live in `content/post/`. Front matter uses YAML with two custom params not in the theme defaults:
- `pinned: true` — floats the post to the top of the homepage listing
- `hidden: true` — excludes the post from all listings

### Layout overrides

Custom layouts in `layouts/` override theme equivalents:
- `layouts/_default/baseof.html` — top-level HTML shell
- `layouts/index.html` — homepage with custom sort logic: pinned posts first, then by date descending, hidden posts excluded
- `layouts/_partials/math.html` — loads MathJax 4 from CDN

### Math rendering

Math is enabled globally (`params.math: true` in `config.yaml`). MathJax 4 handles both inline (`$...$`, `\(...\)`) and display (`$$...$$`, `\[...\]`) LaTeX. The Goldmark renderer is configured with passthrough extensions so Hugo does not escape math delimiters before MathJax processes them.

### Theme submodule

The theme is a git submodule. After cloning, run:

```bash
git submodule update --init --recursive
```

CI does this automatically via `submodules: recursive` in the workflow.
