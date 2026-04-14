---
name: create-pr-description
description: Generates a PR description by reading docs/pull_request_template.md and filling it with bulleted points derived from the git diff, commit log, and changed files on the current branch. Use this skill when the user asks to create a PR description, write a pull request body, or summarise changes for a PR in the BNF OMRAS project.
---

# Create PR Description

To generate a PR description that matches the project template, follow this workflow.

## Step 1: Read the PR Template

Read `docs/pull_request_template.md` to get the exact section headings and format required. Do not invent sections — use only what the template defines.

## Step 2: Gather What Changed

Run the following git commands to understand the work done on this branch:

```bash
# Commits on this branch not yet on main
git log main..HEAD --oneline

# Files changed vs main
git diff main..HEAD --stat

# Full diff for detail
git diff main..HEAD
```

If `main` does not exist or the branch has no upstream, fall back to recent commits:

```bash
git log --oneline -10
git diff HEAD~1..HEAD --stat
```

Read the .gitignore file to not include files which have been ignored, these will not be a part of the commit anyway.

## Step 3: Fill the Template

Map the gathered information to each section from `docs/pull_request_template.md`, keeping things super simple for other devs and not too many little details:

### Summary

One or two sentences describing the overall purpose of the PR. Focus on the **why** — what problem it solves or what value it adds — not a file list.

### Details

A bulleted list of the specific things that were built or changed. Each bullet describes one logical change in plain language.

**Bullet writing rules:**

- Start each bullet with a past-tense verb: "Added", "Removed", "Fixed", "Replaced", "Updated", "Extracted"
- Be specific but concise — one sentence per bullet
- Mention component or feature names, not file paths
- Group related file changes into one meaningful bullet
- Skip noise (import reordering, linter auto-fixes) unless that is the point of the PR
- Aim for 3–8 bullets

## Step 4: Output

Print the completed PR description as a fenced markdown code block so the user can copy it directly.

Do **not** open a PR, create a file, or run `gh pr create` automatically — output the description text only, and wait for the user to confirm before doing anything further.

## Example Output

```markdown
### Summary

Added a design md file to control new component ui designs.

### Details

- Added `DESIGN.md` in the project root.
- Added a templte PR description in docs.
- Created a new skill `create-pr-description` to make human-readable descriptions for PRs.
- Added a command to 
```
