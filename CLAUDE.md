# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this is

A single-file static website for **Circuit Community** (a cat rescue/adoption group, "Cats of Circuit Makati"). The entire site — HTML, CSS, and all images — lives in one file: `circuit-community.html`. There is no build step, no bundler, no JS framework, and no `<script>` tags; the mobile nav dropdown/drawer is done with the CSS checkbox-hack (`#nav-toggle`).

## Commands

There is no package.json, build tool, or test suite. To preview the site, just open the file directly:

```
start circuit-community.html   # Windows: opens in default browser
```

There is nothing to lint or compile. Editing the file *is* the deploy artifact — whatever is committed to `circuit-community.html` is the shippable site.

## Architecture / file layout

- `circuit-community.html` — the entire site. ~950 lines, but ~2.8MB because most images are inlined as `data:image/...;base64,...` URIs directly in `src` attributes (and one background image in the `<style>` block). Do not try to "clean up" these long base64 lines — they are the actual image data.
- `community_logo_orig.png`, `community_logo_160.png`, `community_logo_160.b64.txt` — source/staging assets for the logo used in the page's inlined `<img class="brand-mark">` base64 string. These are reference files for regenerating the embedded base64, not referenced by the HTML at runtime.
- `.agents/skills/`, `.claude/skills/`, `skills-lock.json` — tool-managed skill sync directories (gitignored); not project content.

### Page structure (all in `circuit-community.html`)

Single long page, sectioned with `<section id="...">` anchors linked from the sidebar/mobile nav (`href="#id"`):

- `#top` — hero
- `#cats` — adoptable cats grid (`.cat-grid` / `.cat-card`)
- `#adopt` — adoption process steps
- `#rescue`, `#spay-neuter` — program info (`.info-split`, `.info-list`)
- `#events`, `#volunteers-gallery`, `#kapon-photos` — photo galleries (`.gallery-grid` / `.gallery-item`)
- `#collaborations`, `#medical-needs` — supporting info sections
- `#donate` — donation methods, with per-channel QR anchors: `#qr-gcash`, `#qr-maya`, `#qr-bdo`, `#qr-bpi`, `#qr-paypal`
- `#links`, `#bottom` — footer / footer links

### Styling conventions

- All design tokens are CSS custom properties on `:root` (colors: `--bg`, `--surface`, `--ink`, `--marigold`, `--coral`, `--teal`, etc.; layout: `--sidebar-w`).
- Dark mode is supported two ways simultaneously: `@media (prefers-color-scheme: dark)` (guarded by `:root:not([data-theme="light"])`) for OS-level preference, and an explicit `:root[data-theme="dark"]` block for a manual toggle. When changing a color token, update it in **all three** places (`:root`, the media-query block, and the `[data-theme="dark"]` block) to keep light/dark/auto in sync.
- Fonts are loaded from Google Fonts via `@import` in the `<style>` block: Fraunces (headings/serif), Karla (body), IBM Plex Mono (eyebrows/labels/mono accents).
- Responsive/mobile nav is pure CSS: `#nav-toggle` (hidden checkbox) + `.burger` label + `.mobile-bar` / `.dropdown-menu` — no JS.

## Subagents

- `.claude/agents/content-editor.md` — handles copy/content edits (cat listings, adoption steps, event/volunteer descriptions, donation info, links text) in `circuit-community.html`. Scoped to text only — it does not touch CSS, layout, or base64 image data, and does not run git commands. `.claude/` is otherwise gitignored (tool-synced skill directories), but `.claude/agents/` is explicitly un-ignored so project subagents are tracked and backed up.

## Working with this file

- Because it's one large file, use targeted greps/reads (e.g. by section `id`, class name, or line range) rather than reading it in full — most of its size is base64 image payloads.
- When adding a new image, follow the existing pattern: inline it as a `data:image/<type>;base64,...` URI directly in the `src` (or CSS `url(...)`) rather than adding an external file reference, to keep the site a single deployable file.
