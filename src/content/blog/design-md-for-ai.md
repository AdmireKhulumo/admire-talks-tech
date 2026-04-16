---
title: 'Design.md for better AI frontend code'
description: 'Lorem ipsum dolor sit amet'
pubDate: 'April 16 2026'
heroImage: '../../assets/blog-placeholder-3.jpg'
---

AI isn't great at designing good-looking frontend components out-of-the-box, neither am I. Hence, I recently tried out Awesome Design's markdown files to guide Claude Code into developing this blog's UI components and this is my experience.

The first shot from this blog's Astro template felt pretty generic, so I set out to make it better. I considered tailwind, but setting up the right components and design styles felt like an unnecessary overhead. Doing designs in Figma instead was even worse, complete overkill for what is meant to be a simple blog.

The challenge I wanted to solve: I didn't have a designer, I didn't want to use Figma, and I didn't want to use Tailwing either. Nonentless, I needed a consistent UI style guide to make sure Claude keeps this consistent across my blog's redesign.

One approach I have come across is to embed the website's style guides into a `DESIGN.md` file. This is quite similar to importing your style guide from Figma into Tailwind, keeping your fonts, primary colours, spacing, and so on, consistent across pages. Awesome Design collects a set of style guides meant for AI agents referencing styles of common web platforms. Judging by the theme of this blog, if you can't tell already, I chose Claude (bit of a fanboy here!). 

The underlying concept is simple: you download a DESIGN.md file of the website you wish to use and place it in the root of your codebase, similar to the `CLAUDE.md` or `AGENTS.md` file. Your coding AI agent should pick this up and read it as context when designing a new component, therefore enforcing your style guide.

In my case, I wanted to be more explicit about this, so I used the [skill-creator skill](https://google.com) from Anthropic to make up a custom ui-design skill which the agent would use when making a new desing. That skill specifically references the DESIGN.md file, making it less likely that it is missed when Claude makes up a new component. I then prompted Claude Code to use the skill to redesing the entire blog, and it worked out amazing! Below are images of the initial default design, to the more minimalistic version based on my chosen Awesome Design file.

To be pragmatic, this specific setup won't suit everyone. If you have a designer then you might be better off using [Figma's MCP integration]() to enable your coding agent to pull components directly from there and put them into code. Alternatively, you can have a frontend engineer translate those into reusable Tailwind configs which the coding agent can read and use in the codebase. A custom design skill is still something I could recommend though, just to make sure.

## How to set it up
This is an overview of the steps I took to get my setup running and produce the results above. More details are available in he specific web pages, so I wont duplicate that here.
1. Download and save my chosen design system's DESIGN.md file from Awesome Design
2. Save the DESIGN.md file in the root of my repository
3. Create a custom ui-design skill using the skill-creator skill from Anthropic
4. Prompt Claude Code to redesign my blog using the new skill

## Helpful links and resources
* This blog's GitHub repository with all sample code descibed above (I used this myself). You will find the ui-design skill in `.claude/skills/ui-design/SKILL.md`
* Awesome Design's page
* Skill creator skill from Anthropic
* Astro, the underlying framework used on the blog

Let me know how you use this setup, or if you have something different instead :)