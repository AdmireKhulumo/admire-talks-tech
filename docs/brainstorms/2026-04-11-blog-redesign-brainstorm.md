# Blog Redesign — Brainstorm
**Date:** 2026-04-11
**Author:** Admire Khulumo
**Status:** Ready for planning

---

## What We're Building

A full visual redesign of the Admire Talks Tech Astro blog to implement the warm, editorial design system defined in `DESIGN.md` (the ui-design skill spec). Every surface — global CSS, Header, Footer, blog index, home page, and blog post layout — will be migrated from the current Bear Blog defaults to the Anthropic-inspired warm palette.

---

## Why This Approach

The current site ships the Bear Blog starter CSS: cool blue-gray neutrals, a `#2337ff` accent, multi-layer drop shadows, and an Atkinson font. The `DESIGN.md` spec defines a completely different visual language — warm parchment backgrounds, terracotta accents, Georgia headings, ring-style shadows, and generous spacing. A full redesign (rather than a token-only or component-by-component approach) ensures consistency from day one and avoids a patchwork of old and new styles coexisting.

---

## Key Decisions

### 1. Scope: Full Redesign
Replace `src/styles/global.css` entirely and migrate every component. No old variables will carry over. Components to touch:
- `src/styles/global.css` — full rewrite with warm token layer
- `src/components/Header.astro` — new nav, warm surface, terracotta active state
- `src/components/HeaderLink.astro` — warm active/hover treatment
- `src/components/Footer.astro` — warm gray, no cool-gray gradient
- `src/layouts/BlogPost.astro` — prose typography, warm shadows, Georgia headings
- `src/pages/index.astro` — home page layout with warm post cards
- `src/pages/blog/index.astro` — blog list with warm card treatment

### 2. Font Strategy: Georgia + system-ui (Atkinson removed)
- **Headlines (h1–h3 in blog posts):** `Georgia, 'Times New Roman', serif` — weight 500, line-height 1.10–1.30
- **Body copy and UI (nav, cards, meta):** `system-ui, Arial, sans-serif` — weight 400–500, line-height 1.60
- **Code:** monospace, ~15px, letter-spacing `-0.32px`
- Atkinson WOFF files and the `font` config in `astro.config.mjs` will be removed

### 3. Color Palette: Warm Only, No Cool Grays
| Role | Token | Hex |
|---|---|---|
| Page background | Parchment | `#f5f4ed` |
| Card / elevated surface | Ivory | `#faf9f5` |
| Primary text | Near Black | `#141413` |
| Secondary text | Olive Gray | `#5e5d59` |
| Meta / tertiary | Stone Gray | `#87867f` |
| Brand CTA / accent | Terracotta | `#c96442` |
| Light border | Border Cream | `#f0eee6` |
| Prominent border | Border Warm | `#e8e6dc` |

### 4. Dark Sections: None (Light-Only Throughout)
All pages remain on Parchment `#f5f4ed`. No Deep Dark or Dark Surface sections on any page. The Header and Footer will also use warm light surfaces (not dark).

### 5. Shadows: Ring-First
- Hover/interactive: `0px 0px 0px 1px #d1cfc5` (warm ring, no drop shadow)
- Elevated cards: `rgba(0,0,0,0.05) 0px 4px 24px` (whisper soft)
- No cool-gray or multi-layer box shadows anywhere

### 6. Spacing and Layout
- Body max-width: `~1200px` centered (up from 720px)
- Blog post prose: `720px` max-width for readability
- Base unit: `8px`; major section vertical padding: `80–120px`
- Border radius: buttons/cards 8–12px, hero containers 24–32px

---

## Approach Chosen

**Single PR, full rewrite.** Since the current styles are entirely wrong (wrong palette, wrong fonts, wrong shadows), a full swap is less risky than incremental patching. The implementation order:

1. Rewrite `global.css` — establish warm token layer as CSS custom properties
2. Update `astro.config.mjs` — remove Atkinson font config
3. Update `BaseHead.astro` — remove Atkinson import
4. Migrate `Header.astro` + `HeaderLink.astro`
5. Migrate `Footer.astro`
6. Migrate `BlogPost.astro` layout — prose styles, Georgia headings
7. Migrate `src/pages/index.astro` — home page
8. Migrate `src/pages/blog/index.astro` — blog list

---

## Resolved Questions

- **Scope:** Full redesign confirmed (not incremental or token-only)
- **Fonts:** Georgia + system-ui; Atkinson removed entirely
- **Dark sections:** None — light-only throughout, including Header and Footer
