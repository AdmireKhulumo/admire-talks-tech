---
name: ui-design
description: >
  Design and implement UI components for the admire-talks-tech blog following the project's
  warm, editorial design system. Use this skill whenever designing, building, or modifying
  ANY UI element — hero sections, cards, buttons, navigation, blog post layouts, forms,
  or any visual component. This includes new Astro components, CSS additions, style changes,
  layout adjustments, or any time the words "design", "style", "look", "component", "UI",
  "layout", or "section" appear in the request. Do NOT skip this skill for "small" changes —
  even a button or a single colour choice must follow the system.
---

# UI Design Skill — admire-talks-tech

This project uses a warm, editorial design system inspired by Anthropic's Claude brand.
All design decisions — colours, typography, spacing, shadows, radius — must come from
`DESIGN.md` at the project root. Read it before touching any UI.

## Required: Read DESIGN.md first

Before writing a single line of CSS or Astro markup, read `/DESIGN.md` (project root).
It is the single source of truth. The notes below are a quick-reference cheat sheet, not
a replacement.

## Quick-reference cheat sheet

### Core palette
| Role | Token | Hex |
|---|---|---|
| Page background (light) | Parchment | `#f5f4ed` |
| Card / elevated surface | Ivory | `#faf9f5` |
| Primary text | Anthropic Near Black | `#141413` |
| Secondary text | Olive Gray | `#5e5d59` |
| Tertiary / meta text | Stone Gray | `#87867f` |
| Brand CTA | Terracotta Brand | `#c96442` |
| Light border | Border Cream | `#f0eee6` |
| Prominent border | Border Warm | `#e8e6dc` |
| Dark surface | Dark Surface | `#30302e` |
| Dark page bg | Deep Dark | `#141413` |
| Dark body text | Warm Silver | `#b0aea5` |
| Focus ring (a11y only) | Focus Blue | `#3898ec` |

**No cool grays. Every neutral must have a yellow-brown undertone.**

### Typography
- Headlines: `Georgia` (Anthropic Serif fallback), weight **500 only**, sizes 20–64px
- Body / UI: `system-ui, Arial` (Anthropic Sans fallback), weight 400–500
- Code: monospace, size ~15px, letter-spacing -0.32px
- Body line-height: **1.60** — never go below 1.40
- Heading line-height: 1.10–1.30 (tight but not cramped)

### Spacing scale (base 8px)
`3 4 6 8 10 12 16 20 24 30` px — use multiples of 8 for section-level spacing (80–120px between major sections).

### Border radius scale
| Label | Value | Use |
|---|---|---|
| Sharp | 4px | Minimal inline elements |
| Subtle | 6–8px | Standard buttons, cards |
| Generous | 12px | Primary buttons, inputs |
| Very rounded | 16px | Featured containers, tabs |
| Maximum | 24–32px | Hero containers, media |

**Never use < 6px on buttons or cards.**

### Shadows — ring-first approach
- Interactive states: `0px 0px 0px 1px #d1cfc5` (warm ring, not drop shadow)
- Elevated cards: `rgba(0,0,0,0.05) 0px 4px 24px` (whisper soft)
- Avoid heavy drop shadows — depth comes from background colour shifts and warm ring halos

## How to apply this when building a component

1. **Read DESIGN.md** — always, even for "small" changes.
2. **Pick the right surface**: light sections on Parchment `#f5f4ed`, dark sections on `#141413`. Alternate between them for page rhythm.
3. **Use the colour tokens by name** — "Olive Gray (`#5e5d59`)" not "a gray colour".
4. **Serif for content headings, sans for UI** — do not mix them up.
5. **Ring shadows for interactive elements** — not drop shadows.
6. **Test against every Do/Don't in DESIGN.md §7** before considering the component done.

## Example component starters

**Hero section (light)**
```
Background: Parchment (#f5f4ed)
Heading: Georgia 64px weight 500, line-height 1.10, color #141413
Subheading: system-ui 20px weight 400, line-height 1.60, color #5e5d59
CTA button: bg #c96442, text #faf9f5, radius 12px, ring shadow 0px 0px 0px 1px #c96442
Section padding: 80–120px top/bottom
```

**Feature card (light)**
```
Background: Ivory (#faf9f5)
Border: 1px solid #f0eee6, radius 8px
Shadow: rgba(0,0,0,0.05) 0px 4px 24px
Title: Georgia 25px weight 500, color #141413
Body: system-ui 16px weight 400, line-height 1.60, color #5e5d59
Padding: 24–32px
```

**Dark section**
```
Background: #141413
Heading: Georgia 52px weight 500, line-height 1.20, color #faf9f5
Body: system-ui 17px, line-height 1.60, color #b0aea5
Borders: 1px solid #30302e
```

**Button — primary CTA**
```
bg #c96442, text #faf9f5, padding 8px 16px, radius 12px
ring: 0px 0px 0px 1px #c96442
```

**Button — secondary**
```
bg #e8e6dc, text #4d4c48, padding 0px 12px 0px 8px, radius 8px
ring: 0px 0px 0px 1px #d1cfc5
```

## Astro-specific notes

- Write styles as Astro component `<style>` blocks using CSS custom properties where possible.
- The existing `src/styles/global.css` uses a different (older) palette — **do not inherit those colour variables for new components**; apply the DESIGN.md values directly.
- Tailwind is available via `@tailwindcss/vite` — you may use it, but always verify the resulting colours match the warm palette above (Tailwind's default grays are cool-toned; use arbitrary values like `bg-[#f5f4ed]` when needed).
- Responsive: collapse multi-column grids to single column below 768px; scale heading sizes down progressively (64px → 36px → 25px).
