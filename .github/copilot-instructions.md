## Purpose

This repository is a small static landing site. These instructions help AI coding agents make safe, useful edits quickly by describing the big picture, key files, runtime/testing habits, and integration points discovered in the codebase.

## Big-picture architecture

- Single static HTML page: `tsk.html` is the entire site (no server-side code, no build system). Edits are made directly in that file.
- Client-only responsibilities: UI (CSS + HTML) and small UI behavior in inline JS (see `openAmazon()` at the bottom of `tsk.html`).
- External integrations: Microsoft Clarity (script with id `u4tq55f6zg`) and Cloudflare Web Analytics (CF beacon token). There are also affiliate/shortened Amazon links using `amzn.to`.

## Key files and patterns (examples)

- `tsk.html` — everything: inline CSS in the document head, product cards in a responsive grid (`.product-grid`, `.product-card`, `.section-heading`).
- Inline JS: small functions near the bottom:
  - `openAmazon(searchQuery)` builds `https://amzn.to/${encodeURIComponent(searchQuery)}` and opens it.
- Images: many visuals are embedded as base64 data URIs inside `img` tags (large single-file distribution). Remove or replace data URIs carefully — they bloat the file.

## Developer workflows (how to run & test locally)

- Quick local preview: open `tsk.html` directly in a browser. For a local HTTP server (recommended to test analytics and routing):

```powershell
# from repository root
python -m http.server 8000
# then open http://localhost:8000/tsk.html in your browser (or use Start-Process to open the browser)
Start-Process -FilePath 'msedge' -ArgumentList 'http://localhost:8000/tsk.html'
```

- No build, lint, or test tooling detected. Changes should be small and validated by opening in a browser + running Lighthouse / HTML validator as needed.

## Project-specific conventions and gotchas

- Single-file-first: CSS and JS are inline by convention. If you extract files, update references in `tsk.html` and keep the site lightweight.
- Affiliate links use shortcodes passed to `openAmazon()` (e.g. `openAmazon('4oOPIlh')`). Do not unintentionally break the URL template — the function encodes input then concatenates with `https://amzn.to/`.
- Analytics: two external trackers are present. If you remove or change tokens, be aware of analytics and privacy implications. Tokens are visible in the HTML: Clarity id `u4tq55f6zg`, Cloudflare beacon token in the CF script tag.
- Images: large base64 blobs appear inline. Replacing them with external assets reduces file size but requires hosting and link updates.

## Safe edit checklist for AI agents

1. Only edit `tsk.html` unless the user asks to add files. Keep commits minimal (one feature/fix per PR).
2. When modifying JS, prefer adding small functions; keep the existing `openAmazon()` behavior unless instructed otherwise.
3. If extracting CSS/JS into new files, update `tsk.html` head and ensure paths are relative; test via local server.
4. Validate external links (amazon shortcodes, `cdltrainingtoday.com`) after edits.
5. Avoid changing analytics tokens unless the user explicitly requests it.

## When to ask the user

- If a change will split the single-file site into multiple files. Ask for confirmation before moving large base64 images off-file.
- If you need to add build tooling, tests, or a package manifest — confirm requirements (deployment targets, CI).

---

If you'd like, I can (pick one): extract CSS/JS into separate files and add a tiny dev README with local-run commands; or keep everything inline and optimize images. Which do you prefer?
