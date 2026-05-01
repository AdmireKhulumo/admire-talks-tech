---
title: 'How to Approach AI Engineering Across Your Team'
description: "How to approach AI engineering so your whole team works the same way, covering model choice, shared configs, and compounding learnings across every engineer's machine"
pubDate: 'May 04 2026'
heroImage: '../../assets/claude-folders.png'
---

AI is great at writing code. The problem is when six engineers are all using it differently in the same code repository. Chaos.

### The chaos of six engineers, six AI setups
Call me controlling if you want, but as a senior engineer, enforcing good, standardised coding practices matters a lot to me. It ensures high quality code and maintainability over time. In the chaos of AI engineering, we need some control, a logic to the madness, a clear _way of working_ for AI engineering in a team. It's opinionated. But so is any good way of working. If it sucks, then everyone can contribute to making it better.

### What I've landed on
Here are some ideas I employ to ensure that the team moves cohesively when using AI to code.

#### 1. Pick a single AI model / provider
If one person uses Codex and another uses Claude, their approach and output will vary. Different model providers have different capabilities and I'm firmly in the camp of picking one and using the same across the whole team. It makes billing easier too. We master the strengths of whatever we pick, learn how to work around the weaknesses. In practice, I see very little meaningful difference in the models for day-to-day coding. I prefer to pick one and stick with it across the team.

#### 2. Share the AI md file and config
Whether it's a `CLAUDE.md` or `AGENTS.md` file, commit that git history so that the coding agent has the same context regardless of who is using it. For Claude, I always commit the `.claude` folder in git and only exclude the `.claude/settings.local.json` file which contains more individual settings such as which commands are allowed and which are not. 

#### 3. Use the same skills, plugins, and coding workflow
I prefer to use the same coding plugin across the team. Workflow plugins generally follow the same process: Brainstorm → Plan → Code → Review. Common plugins include [Superpowers](https://github.com/obra/superpowers) and [Compound Engineering](https://github.com/EveryInc/compound-engineering-plugin) by Every Inc. I'm quite biased towards Compound Engineering which includes an extra step after the coding step, Compound. More of this in the next tip. Using the same workflow ensures that we are essentially following the same steps with an equivalent amount of rigor and intentionality in the output code. Nobody skips steps, nobody outsources thinking to the AI. Better already.

#### 4. Share learnings across each agent's run
An agent should get better after each run. As an engineer, when I come across an issue and find a way to solve it, I learn from that and don't (or shouldn't) repeat the same error. That knowledge stacks up over time. An AI agent should also learn from every time an engineer prompts it, or when it makes a mistake. With each engineer coding on a different computer and AI agent, we need a way to share those learnings across every engineer's machine. Every Inc calls this the compound engineering loop, the idea that the agent compounds knowledge from past mistakes and solutions so it doesn't repeat them in the future. This is gold. With that plugin, learnings are stored in `docs/solutions` and committing this to your git history ensures that every engineer's agent has access to solutions learned by agents run by other engineers, thereby spreading the collective knowledge across the team.

---
The good thing is that when the team puts effort into making this way of working better, it improves overall, along with the consistency of the team's AI output. Win, win. 🙌