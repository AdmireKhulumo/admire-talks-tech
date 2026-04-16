---
title: 'Using Design.md as a Figma-free style guide for AI-generated UI'
description: 'How I used a DESIGN.md file and a custom Claude Code skill to give this blog a consistent visual identity without a designer, Figma, or Tailwind.'
pubDate: 'April 16 2026'
heroImage: '../../assets/awesome-design.png'
---

AI isn't great at designing good-looking frontend components out-of-the-box, and neither am I.

### My UI design challenge

This blog's first UI layout came from an Astro template which felt pretty generic. I considered Tailwind, but setting up the right components and design styles felt like an unnecessary overhead. Doing designs in Figma instead was even worse, complete overkill for what is meant to be a simple blog.

That left me at an odd point: I didn't have a designer, I didn't want to use Figma, and I didn't want to use Tailwind either. Nonetheless, I needed a consistent UI style guide to make sure Claude keeps consistent styling across my blog's redesign.

### Saved by DESIGN.md
One approach I came across was to embed the website's style guides into a `DESIGN.md` file. This is quite similar to importing your style guide from Figma into a Tailwind config, keeping your fonts, primary colours, spacing, and so on, consistent across pages. [Awesome Design](https://github.com/VoltAgent/awesome-design-md) is a repository of style guides in markdown (.md) meant for AI agents. The guides are based on designs of popular websites and platforms. Judging by the theme of this blog, if you can't tell already, I chose the Claude theme (bit of a fanboy here!). 

The underlying concept is simple: you download a `DESIGN.md` file of the website you wish to use and place it in the root of your codebase, similar to the `CLAUDE.md` or `AGENTS.md` file. Your coding AI agent should pick this up and read it as context when designing a new component, therefore enforcing your style guide.

In my case, I wanted to be more explicit about this, so I used the [skill-creator skill](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md) from Anthropic to make up a custom [ui-design skill](https://github.com/AdmireKhulumo/admire-talks-tech/blob/main/.claude/skills/ui-design/SKILL.md) which the agent could use when making a new design. The skill specifically references the `DESIGN.md` file, making it less likely that it is missed when Claude generates a new component. I then prompted Claude Code to use the skill to redesign the entire blog, and it worked out amazingly! Below are images of the initial default design, to the more minimalistic version based on my chosen Awesome Design file.

<div style="display:flex;flex-wrap:wrap;gap:1rem;margin:2rem 0;">
  <figure style="flex:1 1 280px;margin:0;">
    <img src="/ui-before.png" alt="Blog design before DESIGN.md" style="width:100%;border-radius:6px;border:1px solid #e5e0d8;" />
    <figcaption style="text-align:center;font-size:0.8rem;color:#888;margin-top:0.5rem;">Before</figcaption>
  </figure>
  <figure style="flex:1 1 280px;margin:0;">
    <img src="/ui-after.png" alt="Blog design after DESIGN.md" style="width:100%;border-radius:6px;border:1px solid #e5e0d8;" />
    <figcaption style="text-align:center;font-size:0.8rem;color:#888;margin-top:0.5rem;">After</figcaption>
  </figure>
</div>

To be realistic, this specific setup won't suit everyone. If you have a designer then you might be better off using [Figma's MCP integration](https://www.figma.com/mcp-catalog/) to enable your coding agent to pull components directly from there and implement them in code. Alternatively, you can have a frontend engineer or UI designer translate those into Tailwind components which the coding agent can read and use in the codebase. A custom design skill is still something I would recommend though, just to make sure.

#### How to set it up
This is an overview of the steps I took to get my setup running and produce the results above. More details are available in the specific websites, so I won't duplicate that here.
1. Download the chosen design system's `DESIGN.md` file from Awesome Design.
2. Add the `DESIGN.md` file in the root of my repository.
3. Create a custom ui-design skill using the skill-creator skill from Anthropic, referencing the `DESIGN.md` file.
4. Prompt Claude Code to redesign my blog using the new skill.

#### Helpful links and resources
* This blog's [GitHub repository](https://github.com/AdmireKhulumo/admire-talks-tech) with all sample code described above (I used this myself). You will find the `ui-design` skill in `.claude/skills/ui-design/SKILL.md`
* [Awesome Design on GitHub](https://github.com/VoltAgent/awesome-design-md)
* [Skill creator skill from Anthropic](https://github.com/anthropics/skills/blob/main/skills/skill-creator/SKILL.md)
* [Astro](https://astro.build/), the underlying framework used on the blog

---
Let me know how you use this setup, or if you have something different instead 🙌