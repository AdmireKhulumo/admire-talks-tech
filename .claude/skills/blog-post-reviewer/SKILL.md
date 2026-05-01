---
name: blog-post-reviewer
description: Reviews blog posts on this site for tone, structure, and title/description alignment. Use this skill whenever the user asks to review a blog article, check a post's tone, or evaluate how well a post's title and description match its content. Trigger even if the user just says "review this article" or "how does this post read?"
---

## Purpose

Review a blog post against the established style of this site — personal, first-person, approachable. Like a senior dev talking to friends over drinks. The gold standard reference is `src/content/blog/design-md-for-ai-ui.md`.

## What to look for

### Tone
Good tone sounds like "I did X and it worked" — personal, specific, story-driven.
Bad tone sounds like "you should do X" or "everyone should" — prescriptive, instructional, abstract.

Watch for:
- First-person voice throughout ("I lean on...", "we use...", "in my experience...")
- Hedging phrases that undercut confidence ("this is a collection of my thoughts", "I guess", "sort of")
- Prescriptive language that should be softened to personal experience ("everyone should" → "we use", "it's better to" → "in my experience")

### Opener
Good: specific, punchy, self-deprecating, or with a concrete hook. Pulls you in immediately.
Bad: generic, abstract, or preachy. Feels like a LinkedIn post intro.

### Headings
Good: evocative, specific, curiosity-triggering ("Saved by DESIGN.md")
Bad: placeholder headings that say nothing ("The Why", "The How", "Introduction")

### Ending
Good: warm sign-off, invitation, or punchy landing line that sends the reader away thinking.
Bad: drops abruptly into a bullet list with no closing thought.

### Alternatives and tradeoffs
Good posts acknowledge that the approach might not suit everyone, or mention alternatives. This builds trust and avoids sounding like a sales pitch.

---

## Output format

Always produce three sections, in this order:

### What works well
Bullet list. Be specific — quote or reference the exact line or section that lands well and say *why* it works.

### What doesn't work well
Bullet list. Be specific — quote the line or section, explain the problem, and suggest the fix in one line. Don't pad this. If something is minor, say so.

### Title & description alignment
One short paragraph. Does the title accurately reflect what the article is actually about? Does the description add something the title doesn't, or just repeat it? Does the content deliver on what both promise? Flag any mismatch between what's promised and what's delivered.

---

## Tone calibration

Read `src/content/blog/design-md-for-ai-ui.md` as the reference before reviewing. Notice:
- The opener is self-deprecating and immediately personal
- It never says "you should" — it says "I did"
- The "Okay, let me stop" type asides feel natural, not performed
- It ends with an invitation, not a bullet list
- Alternatives are acknowledged honestly

Calibrate your review against that bar. Don't apply it rigidly — the goal is to catch where the reviewed post drifts from this voice, not to make every post identical.
