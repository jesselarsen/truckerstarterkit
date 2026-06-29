# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

TruckerStarterKit.com is a static affiliate marketing site for truck driver gear. It's a single-page HTML site deployed to Cloudflare Workers via `wrangler`.

## Deployment

```powershell
npx wrangler deploy
```

The site is deployed as Cloudflare Workers static assets (see `wrangler.jsonc`). The `assets.directory` is `/`, meaning the entire repo root is served. There is no build step — `index.html` is the live production file.

Live site: https://truckerstarterkit.com  
Static assets (hero image, etc.): https://assets.truckerstarterkit.com

## Image Pipeline

Two tools exist for image processing:

**Node.js (product images → WebP for the site):**
```powershell
# Place images in image-processing/, then:
node image-processing/resize.js
# Outputs to image-processing/output/ at 300px and 600px widths, quality 80
```

**Python (product images → base64 embedded `<img>` tags):**
```powershell
python product_image_optimize_embed.py [input_dir] [output_dir] [--quality N] [--size WxH] [--format webp|png|jpg] [--all-formats] [--log]
# Default: reads from script dir, outputs to ./output, 200x200 WebP at quality 75
```
Requires `Pillow`. The Python script writes both the image file and an `.html` file containing the ready-to-paste `<img>` tag with inline base64.

**Favicon generation (PowerShell → inline base64 `<link>` tag):**
```powershell
.\Generate-Favicon.ps1 -InputFile "favicon.png"
# Writes favicon.html and copies the <link> tag to clipboard
```

## Architecture

- **`index.html`** — the entire site. All CSS is inline in `<style>` blocks; no external stylesheet. Product cards use inline base64 images to avoid extra HTTP requests.
- **`wrangler.jsonc`** — Cloudflare Workers config; serves repo root as static assets.
- **`sitemap.xml` / `robots.txt`** — standard SEO files at root, served directly.
- **`icons/`** — all favicon variants (PWA sizes, Apple touch icons, MS tiles) plus `site.webmanifest` and `browserconfig.xml`.
- **`image-processing/`** — source product images and `resize.js`. Outputs go to `image-processing/output/`.
- **`archive/`** — old HTML iterations kept for reference; not served as primary content.
- **Palette variants** — `TSK_*.html` files are design experiments for different color palettes, not production pages.

## Key Conventions

- The canonical production page is `index.html`. The files `tsk.html` and `tsk-updated.html` are drafts/alternates.
- Product images are base64-encoded inline in the HTML (not linked externally) to eliminate render-blocking image requests.
- The hero background image is hosted on `assets.truckerstarterkit.com` (separate Cloudflare asset domain), not inlined.
- Google Fonts are loaded via `<link>` in the `<head>` (DM Sans, Fira Sans, Libre Baskerville, Roboto).
- No JavaScript framework, no bundler, no build step.
