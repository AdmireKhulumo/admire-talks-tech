# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
npm run dev        # Start dev server (localhost:4321)
npm run build      # Build for production (outputs to dist/)
npm run preview    # Preview production build locally
```

No test or lint commands are configured.

## Architecture

This is an **Astro 6** static blog site using TypeScript strict mode.

**Content system:** Blog posts live in `src/content/blog/` as `.md` or `.mdx` files. The collection schema is defined in `src/content.config.ts` — frontmatter fields are `title`, `description`, `pubDate` (required), plus optional `updatedDate` and `heroImage`.

**Routing:** Pages under `src/pages/` map to URL routes. `src/pages/blog/[...slug].astro` handles dynamic blog post routes by querying the content collection.

**Site constants:** Update `src/consts.ts` to change `SITE_TITLE` and `SITE_DESCRIPTION`. Update the `site` URL in `astro.config.mjs` (currently `https://example.com`) for sitemap and RSS to work correctly.

**Fonts:** Custom "Atkinson" font loaded from local WOFF files in `src/assets/fonts/` via `astro.config.mjs` font configuration — no external font CDN.

**Integrations:** `@astrojs/mdx` (MDX support), `@astrojs/sitemap` (auto-generates sitemap on build), `@astrojs/rss` (RSS feed at `/rss.xml`).
