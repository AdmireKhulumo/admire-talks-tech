---
title: 'Setting Up AI Engineering Across Your Team'
description: 'How to structure AI engineering so your whole team works the same way — covering model choice, shared configs, and compounding learnings across every engineer's machine'
pubDate: 'April 28 2026'
heroImage: '../../assets/claude-folders.png'
---

If everyone does whatever they like in a repo, then there will be chaos. We need some control, a logic to the madness, something to standardise things, a clear _ways of working_ for AI engineering in a team.

### The Why
Call me controlling if you want, but as a senior engineer, enforcing good standard coding practices to ensure high quality code and maintainability really matters to me. It makes PR reviews simpler.

This is a collection of my thoughts on how to make sure that everyone produces the same quality of code when using AI. If it works well, then it is great for everyone in the team. If it sucks, then everyone can contribute to making it better. Either way, we move as a team with a clear established way of working with AI engineering.

### The How

### 1. Pick a model / provider
If one person uses Codex and another uses Claude, the output will vary quite substantially. Different model providers have different capabilities and I lean on the camp of picking one and using the same across the whole team. It makes billing easier too. You'll master the strengths of whatever you pick, learn how to work around the weaknesses. In practise, I see very little meaningful difference in the models for day-to-day coding, so pick one and use it across the team.

### 2. Share the AI md file and config
Whether it's `CLAUDE.md` or `AGENTS.md` file, commit that to the git history so that the coding agent has the same context regardless of who is using it. For Claude, this means not excluding the `.claude` folder from git. Instead, you can exclude the `.claude/settings.local.json` file only which contains more individual settings such as which commands are allowed and which are not. 

### 3. Use the same skills, plugins, and coding flow
Everyone should use the same coding plugin. They generally follow the same flow anyway: Brainstorm --> Plan --> Code --> Review. Common plugins include Superpowers and Compound Engineering by Every Inc. I'm quite biased towards Compound Engineering which includes an extra step after the coding step, Compound. More of this in the next tip.

### 4. Share learnings across each agent's run
When one person runs the agent and it comes across issues which they direct it to fix, there's a learning opportunity there. This is the same way we learned too, we made mistakes and found answers and then did not repeat the same mistake. I strongly believe in ensuring that your agent continues to learn, and that those learnings should be shared across agents running on each engineer's computer. Every Inc terms this learning loop as compound engineering, the idea that the agents compounds knowledge from past mistakes and solutions so that it does not repeat them in the future. This is gold. With that plugin, learnings are stored in `docs/solutions` and adding this to your git ensures that every engineer has access to solutions learned by agents run by other engineers. Okay, let me stop with Every Inc, the point here is that it's better to use the same flow and set of skills and optimise those across the team instead of a varied mix.

#### Helpful links and resources
* [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) plugin by Every Inc
* [Superpowers](https://github.com/obra/superpowers) plugin