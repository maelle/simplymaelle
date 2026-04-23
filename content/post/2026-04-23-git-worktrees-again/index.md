---
title: "Git worktrees are now cool"
date: '2026-04-23'
tags:
  - good practice
  - git
slug: git-worktree-again
---

I'm not mad when current AI tools or tips[^aiproof] make Git look cool and important.
Something old and stable to cling to! :hugging_face:
In particular, it's now trendy to have agents work on different tickets in the same project in parallel by using _Git worktrees_.
Git worktrees are even useful for us humans, even if we should probably not work on a dozen things at the same time.

I had learnt about Git worktrees a while ago, but only viewed them as a way to open a read-only view of a past version of a repository:
I have written about [loading different R package versions at once with git worktree](/2024/01/23/git-worktree/).
The exercise about worktrees I added to the saperlipopette R package is very much about [reading too](https://docs.ropensci.org/saperlipopette/reference/exo_worktree.html).

Let me sum up what I learnt about Git worktrees in the last weeks...

## Git worktrees are for any work, not only reading

Let me first admit I have trouble getting used to the idea of a "working tree". 
What Git calls a "working tree" I call "my files" (my _precious_ files).
But knowing what a "working tree" is makes it easier to get used to a "worktree", which is a working tree with some metadata.

You can init/clone your Git repository once and then from there sprout as many working trees as you need.
Only the main one has a full-fledged `.git` folder, the others have a `.git` _file_ with a line like "gitdir: /path-to-the-main-worktree/.git/worktrees/current-worktree" in it.[^inside]
But from any worktrees you can run any Git commands.
You can for instance:
- have a worktree where you fix a bug and another worktree where you write docs.
- create commits and push from either one.

From reading [Julia Evans' bonus comic about Git worktrees](https://wizardzines.com/comics/git-worktree/), I learnt that an advantage of using worktrees rather than cloning the same repo several times is that it's faster, because of the worktrees' sharing a `.git` folder.

## Creating a worktree for a brand-new branch is easy

If you want to create a new worktree for a new branch `feat-bla`, do not run

```
git branch -c feat-bla
git worktree add ../feat-bla feat-bla
```

You can simply run:

```
git worktree add ../feat-bla
```

I learnt that from reading the [manual page for git worktrees](https://git-scm.com/docs/git-worktree), who'd have thought reading docs was useful? 🫠

## Conclusion

Git worktrees are cool, and they're not even fancy,
even if your AI service or orchestrator might manage them for you.
They're sort of a... low-hanging fruit I guess. :speak_no_evil:

[^aiproof]: For instance, the post ["Becoming an AI-proof software engineer"](https://deadsimpletech.com/blog/ai_proof_engineer) by Iris Meredith states "Being an effective user of git makes software development much less painful and importantly allows you to collaborate with other developers". It also recommends writing. :smile_cat:
[^inside]: I am very proud of having gone inside `.git` to prep this post. 🫡