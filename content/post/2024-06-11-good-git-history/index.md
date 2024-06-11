---
title: "Hack your way to a good Git history"
date: '2024-06-11'
slug: rewrite-git-history
output: hugodown::hugo_document
tags:
  - good practice
  - git
rmd_hash: f6adddb29482cf1a

---

I've now explained on this blog why it's important to have [small, informative Git commits](/2024/06/03/small-commits/)[^1] and how I've realized that polishing history can happen in a [second phase of work in a branch](/2023/12/07/two-phases-git-branches/). However, I've more or less glossed over *how* to craft the history in a branch once you're done with the work.

I've entitled this post *"Hack your way to a good Git history"* because writing the history after the fact can feel like cheating, but the truth is, it's not.

## To branch or not to branch

With the first method of re-writing history I'll present, you could get away with working on the default branch as long as you do not push. However, I'd really recommend creating a branch before you start working on whatever task you've assigned yourself, be it a bug fix, new feature or some refactoring. It feels safer, and it's a good habit to take.

## "The repeated amend":tm:

[^1]: I should also have added "atomic", because if you can't load your project at a given commit because of a missing parenthesis, then you can't run Git bisect for instance.

