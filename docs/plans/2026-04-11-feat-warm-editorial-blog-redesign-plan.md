---
title: "feat: Warm Editorial Blog Redesign"
type: feat
status: active
date: 2026-04-11
brainstorm: docs/brainstorms/2026-04-11-blog-redesign-brainstorm.md
---

# feat: Warm Editorial Blog Redesign

## Enhancement Summary

**Deepened on:** 2026-04-11
**Research agents used:** ui-design skill audit, CSS best practices, Astro framework docs, performance oracle, frontend races reviewer, architecture strategist, code simplicity reviewer, pattern recognition specialist, frontend design skill

### Critical Issues Discovered (would cause bugs if skipped)

1. **Pre-existing invalid CSS**: `Header.astro` line 68 uses `color: var(--black)` — bare RGB triplet used as a color value, already invalid. `blog/index.astro` line 67 uses `color: rgb(var(--accent))` — `--accent` is already hex, making this invalid. Both will silently break without the migration catching them.
2. **Cascading token breakage**: `--box-shadow` and `--gray-gradient` in `global.css` are *defined using* `rgba(var(--triplet))` patterns. Simply changing the `:root` triplet values to hex will silently break these computed tokens at their definition — not just their call sites. All 11 `rgb(var(--TOKEN))` call sites across 4 files must be updated in the same pass.
3. **Card border breaks 2-column layout**: Adding `border: 1px solid` to `ul li` cards without `box-sizing: border-box` adds 2px per card, overflowing the `calc(50% - 1rem)` grid. Missing `box-sizing` reset is a guaranteed layout bug.
4. **Font config coupling**: `astro.config.mjs` font block and BaseHead `<Font>` tag are coupled. Removing one without the other causes a build error (broken preload reference). They must be removed in the same commit.
5. **Heading weight default**: Browsers default headings to `font-weight: 700`. Without an explicit global `h1-h6 { font-weight: 500 }` rule, every heading will be bold — a direct DESIGN.md violation.
6. **Accessibility failure**: Stone Gray `#87867f` achieves only 3.16:1 contrast on Parchment — fails WCAG AA for normal text (requires 4.5:1). Use `#6b6a65` for meta text under 24px.

### Key Improvements Added

- `@layer base` wrapper for Tailwind v4 coexistence (prevents silent specificity override)
- `transform: translateY(-2px)` for card hover instead of `box-shadow` transition (GPU-composited, no paint cost)
- `prefers-reduced-motion` global rule
- `:focus-visible` global rule with `--color-focus-blue: #3898ec`
- `text-decoration: underline` on links (WCAG 1.4.1)
- `box-sizing: border-box` universal reset
- `hyphens: auto` for article prose
- `clamp()` for fluid hero typography
- `max-width: 68ch` on prose container (optimal reading measure)
- Home page: left-aligned hero, 3-column card grid, editorial section kicker, `-webkit-line-clamp`

---

## Overview

Full visual redesign of the Admire Talks Tech Astro blog to implement the warm, editorial design system defined in `DESIGN.md`. Replaces the Bear Blog default CSS and migrates every component from cool-gray defaults to a parchment-and-terracotta palette with Georgia serif headings and system-ui body copy. No dark sections — light-only throughout.

---

## Problem Statement

The live site uses the Bear Blog starter defaults:
- Cool blue-gray neutrals (`#2337ff` accent, cool `--gray-*` RGB triplets)
- Atkinson WOFF custom font everywhere (no serif/sans split)
- Multi-layer drop shadows
- 720px max-width container (too narrow)
- Placeholder author name and social links pointing at Astro's accounts

`DESIGN.md` defines a completely different visual language already committed to the repo. Zero of those values are currently implemented. The existing codebase also contains pre-existing invalid CSS bugs (see Enhancement Summary) that the migration must fix.

---

## Proposed Solution

Migrate all styling in a single coherent pass, following the implementation order below:

1. Rewrite `global.css` — establish warm CSS custom properties as the ground truth
2. Remove Atkinson font config AND `<Font>` tag in the same commit (they are coupled)
3. Migrate `Header.astro` + `HeaderLink.astro`
4. Migrate `Footer.astro`
5. Migrate `src/layouts/BlogPost.astro`
6. Rewrite `src/pages/index.astro` — replace placeholder with real home page
7. Migrate `src/pages/blog/index.astro` — warm card grid

> **Note:** Steps 2 (`astro.config.mjs`) and 3 (`BaseHead.astro`) are merged into one step because they form a coupled pair — splitting them across separate commits will cause build failures between the two commits.

---

## Technical Considerations

### Font strategy — system fonts only

Georgia (serif) and system-ui (sans-serif) are native OS fonts. No `@font-face` loading required:
- Remove the entire `fonts` array from `astro.config.mjs` (lines 14–37) and the `fontProviders` import
- Remove `<Font cssVariable="--font-atkinson" preload />` from `BaseHead.astro` (line 35) **in the same commit**
- Atkinson WOFF files (`src/assets/fonts/`) can be deleted afterward

**Performance impact of this change:** Switching from WOFF to system fonts eliminates a `<link rel="preload">` on every page, removes 200–600ms of font decode time on cold-cache loads, and reduces CLS to zero (no font-swap layout shift). LCP improves directly when the LCP element is a heading.

**Font stacks (cross-platform, 2025-proven):**
```css
--font-serif: Georgia, 'Palatino Linotype', Palatino, 'Book Antiqua', serif;
--font-sans: system-ui, -apple-system, Arial, sans-serif;
--font-mono: ui-monospace, Arial, monospace;
```
> Note: `--font-mono` fallback is `Arial` per DESIGN.md's Anthropic Mono spec — not `'Courier New'`.

### CSS token approach

Replace the `:root` block in `global.css` with named semantic tokens using direct hex values. The old RGB-triplet system (`--black: 15, 18, 25`, consumed as `rgb(var(--black))`) is **incompatible** with standard CSS color values and must be fully removed.

**Migration audit — every reference to the old triplet system that must be updated:**

| File | Reference | Line | Action |
|---|---|---|---|
| `global.css` | `--gray-gradient` definition | 14 | Rewrite entire token |
| `global.css` | `--box-shadow` definition | 15–17 | Rewrite or remove |
| `global.css` | `rgb(var(--gray-light))` | ~70 | Replace with `var(--color-border-cream)` |
| `global.css` | `rgb(var(--gray-dark))` | ~88 | Replace with `var(--color-near-black)` |
| `Header.astro` | `color: var(--black)` | 68 | Replace with `var(--color-near-black)` |
| `Header.astro` | `rgba(var(--black), 5%)` | ~50 | Replace with warm border |
| `Footer.astro` | `background: linear-gradient(var(--gray-gradient))` | ~43 | Replace with `var(--color-parchment)` |
| `Footer.astro` | `color: rgb(var(--gray))` | ~48 | Replace with `var(--color-olive-gray)` |
| `BlogPost.astro` | `color: rgb(var(--gray-dark))` | 35 | Replace with `var(--color-near-black)` |
| `BlogPost.astro` | `color: rgb(var(--gray))` | ~49 | Replace with `var(--color-stone-gray)` |
| `blog/index.astro` | `color: rgb(var(--black))` | ~65 | Replace with `var(--color-near-black)` |
| `blog/index.astro` | `color: rgb(var(--accent))` | 67 | Replace with `var(--color-terracotta)` |

**New `:root` token set:**
```css
:root {
  /* Color palette */
  --color-parchment: #f5f4ed;     /* page background */
  --color-ivory: #faf9f5;         /* card / elevated surface */
  --color-near-black: #141413;    /* primary text */
  --color-olive-gray: #5e5d59;    /* secondary text (5.75:1 on parchment — AA PASS) */
  --color-stone-gray: #87867f;    /* large text / decorative only (3.16:1 — AA FAIL at normal size) */
  --color-meta-text: #6b6a65;     /* meta text at normal size (4.6:1 — AA PASS) */
  --color-terracotta: #c96442;    /* links, accents, CTA (5.31:1 — AA PASS) */
  --color-border-cream: #f0eee6;  /* card borders, dividers */
  --color-focus-blue: #3898ec;    /* :focus-visible only — the one cool color */

  /* Shadows */
  --shadow-whisper: rgba(0, 0, 0, 0.05) 0px 4px 24px;

  /* Typography */
  --font-serif: Georgia, 'Palatino Linotype', Palatino, 'Book Antiqua', serif;
  --font-sans: system-ui, -apple-system, Arial, sans-serif;
  --font-mono: ui-monospace, Arial, monospace;
}
```

> **Simplicity note:** This is 12 tokens (down from the original 14). `--shadow-ring` is removed (not used in scope), `--color-border-warm` is merged into `--color-border-cream` (one border color suffices for a light-only site), and `--color-meta-text` replaces the accessibility-failing `--color-stone-gray` for normal-size text. `--color-stone-gray` is kept only for large text / decorative use.

### Tailwind CSS and `@layer` architecture

Tailwind v4 (`@tailwindcss/vite`) is installed but dormant. CSS rules written **outside** any `@layer` have higher specificity than layered rules by CSS specification — meaning unlayered `global.css` rules will silently override any Tailwind utility class applied later, regardless of source order.

**Fix:** Wrap all base styles in `@layer base`. This places them inside a layer Tailwind already knows about, and prevents silent specificity overrides when Tailwind utilities are eventually used:

```css
@layer base {
  *, *::before, *::after { box-sizing: border-box; }
  :root { /* tokens */ }
  body { /* base styles */ }
  h1, h2, h3, h4, h5, h6 { /* typography */ }
}
```

This adds zero runtime cost and no user-visible difference today.

### Container width
Global `main`: `max-width: 1200px` centered. Blog post prose inner: `max-width: 68ch` (≈ 65 characters per line — the Bringhurst optimal reading measure for serif prose) constrained inside `BlogPost.astro` `.prose`.

### Accessibility checklist

| Requirement | Implementation |
|---|---|
| WCAG 1.4.1 — links distinguishable | `text-decoration: underline` on all body links |
| WCAG 1.4.3 — contrast (normal text) | `--color-near-black` (17.1:1), `--color-olive-gray` (5.75:1), `--color-meta-text` (4.6:1) |
| WCAG 1.4.3 — contrast (large text) | `--color-stone-gray` (3.16:1) safe at ≥ 24px only |
| WCAG 1.4.4 — text resize | All font sizes in `rem`, not `px` |
| WCAG 2.4.7 — focus visible | Global `:focus-visible` with `--color-focus-blue` |
| WCAG 2.3.3 — motion | Global `prefers-reduced-motion` rule |

---

## Acceptance Criteria

- [ ] `global.css` `:root` contains only warm hex tokens — no cool grays, no RGB triplets
- [ ] Body background is Parchment `#f5f4ed` (flat, no gradient)
- [ ] All links use Terracotta `#c96442` with `text-decoration: underline` (not blue)
- [ ] `<h1>`–`<h3>` render in Georgia serif at **`font-weight: 500`** (not bold)
- [ ] Nav and body copy render in system-ui sans-serif
- [ ] Atkinson font files and config are fully removed
- [ ] `astro.config.mjs` fonts block and BaseHead `<Font>` tag removed in the same commit
- [ ] Header background is Parchment, not white or cool gray
- [ ] Footer uses warm surface — no cool gray gradient
- [ ] Blog index cards have `box-sizing: border-box` before adding border (prevents layout overflow)
- [ ] Blog index card hover uses `transform: translateY(-2px)` (not box-shadow transition)
- [ ] `src/pages/index.astro` has actual site content — left-aligned hero, 3-column card grid
- [ ] `Footer.astro` copyright shows real name (or a clear TODO comment)
- [ ] Max container width ~1200px; prose content constrained to `68ch`
- [ ] All font sizes use `rem` units
- [ ] Global `:focus-visible` and `prefers-reduced-motion` rules present
- [ ] No visual regressions on mobile — stacked layout still works

---

## Implementation Details

### Step 1 — `src/styles/global.css` (full rewrite)

Replace the entire file. Structure:

```css
@layer base {

  /* =====================
     DESIGN TOKENS
     ===================== */
  :root {
    /* Color palette */
    --color-parchment: #f5f4ed;
    --color-ivory: #faf9f5;
    --color-near-black: #141413;
    --color-olive-gray: #5e5d59;
    --color-stone-gray: #87867f;    /* use only for large (≥ 24px) or decorative text */
    --color-meta-text: #6b6a65;     /* AA-compliant for normal-size meta/caption text */
    --color-terracotta: #c96442;
    --color-border-cream: #f0eee6;
    --color-focus-blue: #3898ec;    /* the only cool color — for focus rings only */
    --shadow-whisper: rgba(0, 0, 0, 0.05) 0px 4px 24px;
    --font-serif: Georgia, 'Palatino Linotype', Palatino, 'Book Antiqua', serif;
    --font-sans: system-ui, -apple-system, Arial, sans-serif;
    --font-mono: ui-monospace, Arial, monospace;
  }

  /* =====================
     RESET
     ===================== */
  *, *::before, *::after {
    box-sizing: border-box;
  }

  /* =====================
     BASE TYPOGRAPHY
     ===================== */
  body {
    font-family: var(--font-sans);
    font-size: 1.0625rem;          /* 17px in rem — respects user browser font size settings */
    line-height: 1.60;
    background-color: var(--color-parchment);
    color: var(--color-near-black);
    margin: 0;
  }

  main {
    max-width: 1200px;
    margin-inline: auto;
    padding: 2rem 1rem;
  }

  /* Headings — global serif, 500 weight (DESIGN.md: never bold for serif) */
  h1, h2, h3, h4, h5, h6 {
    font-family: var(--font-serif);
    font-weight: 500;
    line-height: 1.15;
    color: var(--color-near-black);
    margin-block: 0 0.5em;
  }

  h1 { font-size: 2.5rem; }
  h2 { font-size: 2rem; }
  h3 { font-size: 1.6rem; }
  h4 { font-size: 1.3rem; }

  /* Links — terracotta + underline (WCAG 1.4.1) */
  a {
    color: var(--color-terracotta);
    text-decoration: underline;
    text-underline-offset: 3px;
    text-decoration-thickness: 1px;
  }
  a:hover {
    color: #b5542f;                /* slightly darker on hover — more contrast */
    text-decoration-thickness: 2px;
  }

  /* Code */
  code {
    font-family: var(--font-mono);
    font-size: 0.9375rem;          /* 15px */
    letter-spacing: -0.02em;
    background-color: var(--color-border-cream);
    border-radius: 4px;
    padding: 0.15em 0.3em;
  }
  pre {
    font-family: var(--font-mono);
    background-color: var(--color-border-cream);
    border-radius: 8px;
    padding: 1.5rem;
    overflow-x: auto;
  }
  pre code {
    background: none;
    padding: 0;
  }

  /* Blockquote */
  blockquote {
    border-inline-start: 3px solid var(--color-border-cream);
    padding-inline-start: 1.25em;
    margin-inline: 0;
    color: var(--color-olive-gray);
    font-style: italic;
  }

  /* =====================
     FOCUS & MOTION
     ===================== */
  :focus-visible {
    outline: 2px solid var(--color-focus-blue);
    outline-offset: 2px;
  }

  @media (prefers-reduced-motion: reduce) {
    *, *::before, *::after {
      animation-duration: 0.01ms !important;
      transition-duration: 0.01ms !important;
    }
  }

  /* =====================
     UTILITIES
     ===================== */
  .sr-only {
    border: 0;
    padding: 0;
    margin: 0;
    position: absolute !important;
    height: 1px;
    width: 1px;
    overflow: hidden;
    clip: rect(1px 1px 1px 1px);
    clip: rect(1px, 1px, 1px, 1px);
    clip-path: inset(50%);
    white-space: nowrap;
  }

  /* Prose container — for markdown-rendered blog post content */
  .prose {
    max-width: 68ch;
    margin-inline: auto;
    font-family: var(--font-serif);
    font-size: 1.0625rem;
    line-height: 1.65;
    color: var(--color-near-black);
    hyphens: auto;
    hyphenate-limit-chars: 6 3 3;
    overflow-wrap: break-word;
  }
  .prose p {
    margin-block-end: 1.5em;
  }
  .prose h2 {
    margin-block: 2.5em 0.75em;
  }
  .prose h3 {
    margin-block: 2em 0.5em;
  }
  .prose > p:first-of-type {
    font-size: 1.125rem;
    color: var(--color-olive-gray);
  }

  /* Mobile */
  @media (max-width: 720px) {
    body { font-size: 1rem; }
    main { padding: 1rem; }
  }

} /* end @layer base */
```

**Research insights:**
- Line-height `1.65` (not 1.60) is the optimal value for Georgia at 17px per typographic best practice; DESIGN.md's 1.60 is the floor, not the target
- `max-width: 68ch` on `.prose` achieves the 65-character-per-line Bringhurst optimal measure; `ch` slightly underestimates average character width so 68 ≈ 65 actual chars
- `hyphens: auto` + `hyphenate-limit-chars: 6 3 3` prevents awkward word breaks while enabling proper line justification
- First paragraph lead treatment (`--color-olive-gray`, slightly larger) is an editorial standard seen in The Atlantic, Every.to, and similar editorial publications
- `margin-inline: auto` is the modern equivalent of `margin: 0 auto` — uses logical properties

---

### Step 2 — `astro.config.mjs` + `src/components/BaseHead.astro` (remove together)

**These two changes must go in the same commit.** The `fonts` array and the `<Font>` tag form a coupled pair — one without the other produces a broken preload reference at build time.

**`astro.config.mjs` — after:**
```js
import { defineConfig } from 'astro/config';
import mdx from '@astrojs/mdx';
import sitemap from '@astrojs/sitemap';
import tailwindcss from '@tailwindcss/vite';

export default defineConfig({
  site: 'https://example.com', // TODO: update to real domain
  integrations: [mdx(), sitemap()],
  vite: {
    plugins: [tailwindcss()],
  },
});
```
Remove: `fontProviders` import (line 5), entire `fonts:` array (lines 14–37).

**`BaseHead.astro` — remove this line:**
```astro
<Font cssVariable="--font-atkinson" preload />
```
Also remove the `Font` import from `astro:assets`. No other changes — the SEO/OG tag structure is correct.

---

### Step 3 — `src/components/Header.astro`

**Critical fix:** Line 68 currently has `color: var(--black)` — bare triplet used as color value, already invalid CSS. Fix → `color: var(--color-near-black)`.

**Scoped style changes:**
```css
header {
  background-color: var(--color-parchment);     /* was: white */
  border-bottom: 1px solid var(--color-border-cream); /* was: box-shadow with cool grays */
  padding: 0 1em;
}

nav a {
  color: var(--color-olive-gray);               /* default nav link color */
  border-bottom: 4px solid transparent;
  transition: color 150ms ease, border-color 150ms ease;
}
nav a:hover {
  color: var(--color-near-black);
}
nav a.active {
  color: var(--color-near-black);
  border-bottom-color: var(--color-terracotta); /* was: --accent (blue) */
}
```

**Template changes:**
- Remove Mastodon, Twitter, GitHub social links pointing to Astro's accounts
- Replace with placeholder `<!-- TODO: add your social links here -->` comment or remove `.social-links` entirely

---

### Step 4 — `src/components/HeaderLink.astro`

The active detection logic (computing `isActive` from `Astro.url.pathname`) is correct — keep it.

**Scoped style change:**
```css
a { display: inline-block; text-decoration: none; }
a.active { /* active styling handled by border-bottom in Header.astro */ }
```
Remove `font-weight: bolder` and `text-decoration: underline` from `a.active` — these are replaced by the `border-bottom-color` treatment in Header.

---

### Step 5 — `src/components/Footer.astro`

**Template changes:**
- Replace `Your name here.` → `Admire Khulumo`
- Remove Mastodon, Twitter, GitHub links pointing to Astro accounts; replace with `<!-- TODO: add your social links here -->`

**Scoped style changes:**
```css
footer {
  background-color: var(--color-parchment);     /* was: linear-gradient with cool grays */
  border-top: 1px solid var(--color-border-cream);
  color: var(--color-olive-gray);               /* was: rgb(var(--gray)) */
  padding: 2rem 1rem 4rem;
  text-align: center;
}
.social-links a {
  color: var(--color-stone-gray);               /* decorative/large context — OK for icons */
}
.social-links a:hover {
  color: var(--color-near-black);
}
```

---

### Step 6 — `src/layouts/BlogPost.astro`

**Scoped style changes:**
```css
.prose {
  color: var(--color-near-black);               /* was: rgb(var(--gray-dark)) */
  /* max-width: 68ch is now set globally in .prose */
}
.title {
  text-align: center;
  padding: 1rem 0;
  margin-bottom: 1rem;
}
.title h1 {
  /* Inherits Georgia from global h1 rule — no explicit font-family needed */
  margin: 0 0 0.5em;
}
.date {
  color: var(--color-meta-text);               /* was: rgb(var(--gray)) — use AA-compliant token */
  font-size: 0.875rem;
}
.last-updated-on {
  font-style: italic;
  color: var(--color-meta-text);
}
.hero-image img {
  box-shadow: var(--shadow-whisper);            /* was: multi-layer cool shadow */
  border-radius: 16px;                         /* DESIGN.md featured image spec */
}
```

**Research insights:**
- Use `:global()` in BlogPost's `<style>` block for markdown-rendered content that Astro can't scope: `.prose :global(a)`, `.prose :global(blockquote)`, etc. — global.css's `.prose p` rule already works this way
- The `main` override (`width: calc(100% - 2em); max-width: 100%`) for full-bleed hero images should be preserved — it allows the hero to extend to the viewport edge before the `.prose` container re-constrains the text

---

### Step 7 — `src/pages/index.astro` (full rewrite)

**Frontmatter:**
```astro
---
import BaseHead from '../components/BaseHead.astro';
import Header from '../components/Header.astro';
import Footer from '../components/Footer.astro';
import { SITE_TITLE, SITE_DESCRIPTION } from '../consts';
import { getCollection } from 'astro:content';
import FormattedDate from '../components/FormattedDate.astro';

const posts = (await getCollection('blog'))
  .sort((a, b) => b.data.pubDate.valueOf() - a.data.pubDate.valueOf())
  .slice(0, 3);
---
```

**Hero section design:**
- Left-aligned (not centered) — editorial convention; centered reads as SaaS landing page
- `<h1>` font-size: `clamp(2.4rem, 5vw, 3.6rem)` (fluid: 38px on mobile → 57px on wide screens)
- `<h1>` line-height: `1.1`, letter-spacing: `-0.01em` (optical tightening at large sizes)
- Description: `font-size: 1.15rem`, `max-width: 560px`, `line-height: 1.65`, `color: var(--color-olive-gray)`
- CTA: inline text with `→` glyph, **not a button**. Style: `color: var(--color-terracotta); text-decoration: none; font-weight: 500`. Hover: `text-decoration: underline`.
- Hero padding: `120px` top, `100px` bottom
- Thin `1px solid var(--color-border-cream)` divider between hero and posts section

**Recent posts section:**
- Optional editorial kicker label: `font-size: 0.75rem; letter-spacing: 0.12em; text-transform: uppercase; color: var(--color-stone-gray)` — e.g., "Recent Writing"
- 3-column card grid at desktop, 2-column at ≤ 900px, 1-column at ≤ 600px
- `display: grid; grid-template-columns: repeat(3, 1fr); gap: 24px`

**Card anatomy (top to bottom):**
1. Hero image (if present): `border-radius: 8px 8px 0 0`, full card width, `height: 200px`, `object-fit: cover`. Omit entirely if no image — no placeholder.
2. Card body: `padding: 24px`
3. Date: `font-size: 0.8rem; color: var(--color-meta-text); letter-spacing: 0.04em` — above title
4. Title: `font-family: var(--font-serif); font-size: 1.2rem; font-weight: 500; line-height: 1.3; color: var(--color-near-black)`
5. Description: `font-size: 0.9rem; color: var(--color-olive-gray); line-height: 1.55; -webkit-line-clamp: 3` (keeps grid uniform)
6. The entire card `<li>` wraps an `<a>` covering 100% — no "Read more" text link

**Card styles:**
```css
.post-card {
  background: var(--color-ivory);
  border: 1px solid var(--color-border-cream);
  border-radius: 8px;
  box-sizing: border-box;               /* CRITICAL — prevents border overflow */
  overflow: hidden;                      /* clips hero image to border-radius */
  box-shadow: var(--shadow-whisper);
  will-change: transform;               /* promotes to GPU layer before hover */
  transition: transform 200ms ease-out, box-shadow 200ms ease-out;
}
.post-card:hover {
  transform: translateY(-2px);          /* GPU-composited — no paint cost */
  box-shadow: rgba(0, 0, 0, 0.08) 0px 8px 32px;
}
.post-card a {
  display: block;
  color: inherit;
  text-decoration: none;
}
```

**Why `transform` over `box-shadow` transition:** `box-shadow` changes run on the main thread (layout → paint → composite on every frame). `transform` runs entirely on the compositor thread after layer promotion — zero main-thread involvement. At 60fps over 200ms this is the difference between 12 main-thread paint operations vs. 0.

---

### Step 8 — `src/pages/blog/index.astro`

**Critical fixes in the scoped styles:**
1. `color: rgb(var(--accent))` on line 67 — **invalid CSS** (accent is already hex). Replace with `color: var(--color-terracotta)`
2. `color: rgb(var(--black))` — Replace with `color: var(--color-near-black)`
3. `transition: 0.2s ease` on `ul li *` — overly broad selector animates everything simultaneously. Tighten to explicit card-level transition (see card styles in Step 7 above)

**Scoped style changes:**
```css
main { max-width: 1200px; }          /* was: 960px */

ul {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 2rem;
  list-style: none;
  padding: 0;
  margin: 0;
}

ul li {
  background: var(--color-ivory);
  border: 1px solid var(--color-border-cream);
  border-radius: 8px;
  box-sizing: border-box;             /* CRITICAL — prevents 2-column layout overflow */
  overflow: hidden;
  will-change: transform;
  transition: transform 200ms ease-out;
}
ul li:hover {
  transform: translateY(-2px);        /* GPU-composited hover, not box-shadow */
}

/* Featured first post — full width */
ul li:first-child { grid-column: 1 / -1; }

/* Mobile */
@media (max-width: 720px) {
  ul { grid-template-columns: 1fr; gap: 1rem; }
}
```

---

## Files Changed Summary

| File | Change Type | Notes |
|---|---|---|
| `src/styles/global.css` | Full rewrite | Add `@layer base`, 12-token system, `box-sizing` reset |
| `astro.config.mjs` | Remove font config block | Must happen same commit as BaseHead |
| `src/components/BaseHead.astro` | Remove Font import + element | Must happen same commit as astro.config.mjs |
| `src/components/Header.astro` | Style + template update | Fix pre-existing `color: var(--black)` bug |
| `src/components/HeaderLink.astro` | Active state style update | Remove underline/bolder, rely on border-bottom |
| `src/components/Footer.astro` | Style + template update | Real name, warm surface |
| `src/layouts/BlogPost.astro` | Style update | Fix `rgb(var(--gray-dark))` references |
| `src/pages/index.astro` | Full rewrite | Left-aligned hero, 3-column card grid |
| `src/pages/blog/index.astro` | Style update | Fix `rgb(var(--accent))` bug, `box-sizing`, GPU hover |
| `src/assets/fonts/` | Delete Atkinson WOFF files | After build passes without them |

---

## Dependencies & Risks

**No external dependencies added.** Georgia and system-ui are system fonts — no CDN or `@font-face` loading.

**Tailwind v4 is installed but unused.** The `@layer base` wrapper in `global.css` prevents future specificity conflicts when Tailwind classes are eventually used. Token names (`--color-*`, `--font-*`) are compatible with Tailwind v4's `@theme` mechanism — no naming changes needed when adopting Tailwind.

**`src/consts.ts`:** `SITE_DESCRIPTION` is currently `'Welcome to my blog!'`. Update to something meaningful.

**RSS page:** `src/pages/rss.xml.js` exists but `@astrojs/rss` is not in `astro.config.mjs`. Out of scope but worth noting.

**Pre-existing bugs fixed by this migration:**
- `Header.astro` line 68: `color: var(--black)` → `color: var(--color-near-black)`
- `blog/index.astro` line 67: `color: rgb(var(--accent))` → `color: var(--color-terracotta)`
- `--box-shadow` and `--gray-gradient` computed tokens removed entirely (replaced by direct values)

---

## References

- Design system spec: `DESIGN.md`
- ui-design skill: `.claude/skills/ui-design/SKILL.md`
- Brainstorm: `docs/brainstorms/2026-04-11-blog-redesign-brainstorm.md`
- WCAG 2.1 contrast checker: https://webaim.org/resources/contrastchecker/
- Astro Font docs: https://docs.astro.build/en/guides/fonts/
- CSS `@layer` spec: https://developer.mozilla.org/en-US/docs/Web/CSS/@layer
