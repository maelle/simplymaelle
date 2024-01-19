---
title: "Reading notes on Pro Git by Scott Chacon"
date: '2024-01-19'
slug: pro-git-scott-chacon-reading-notes
output: hugodown::hugo_document
tags:
  - good practice
  - books
  - git
rmd_hash: c3f0a26510a7b6c7

---

As mentioned about a million times on this blog, last year I read [Git in practice by Mike McQuaid](/2023/11/01/reading-notes-git-in-practice/) and it changed my life -- not only giving me bragging rights about the reading itself. :sweat_smile: I decided to give [Pro Git by Scott Chacon](https://git-scm.com/book/en/v2) a go too. It is listed in the [resources section](https://happygitwithr.com/resources) of the excellent "Happy Git with R" by Jenny Bryan, Jim Hester and others. For unclear reasons I bought the first edition instead of the second one.

## Git as an improved filesystem

In the Chapter 1 (Getting Started), I underlined:

> "\[a\] mini filesystem with some incredibly powerful tools built on top of it".

## Awesome diagrams

One of my favorite parts of the book were the diagrams such as the one illustrating "Git stores data as snapshots of the project over time".

## A reminder of why we use Git

> "after you commit a snapshot into Git, it is very difficult to lose, especially if you regularly push your database to another repository."

The last chapter in the book, "Git internals", include a "Data recovery" section about [`git reflog`](https://maelle.github.io/saperlipopette/reference/exo_time_machine.html) and [`git fsck`](https://git-scm.com/docs/git-fsck).

## One can negate patterns in `.gitignore`

I did not know about this [pattern format](https://git-scm.com/docs/gitignore#_pattern_format). In hindsight it is not particularly surprising.

## `git log` options

The `format` option lets one tell Git how to, well, format the log. I mostly interact with the Git log through a GUI or the gert R package, but that's good to know.

The book also describes how to filter commits in the log (by date, author, committer). I also never do that with Git itself, but who knows when it might become useful.

## A better understanding of branches

I remember reading "branches are cheap" years ago and accepting this as fact without questioning the reason behind the statement. Now thanks to reading the "Git Branching" chapter, but also Julia Evans' blog post ["git branches: intuition & reality"](https://jvns.ca/blog/2023/11/23/branches-intuition-reality/), I know they are cheap because they are just a pointer to a commit.

Likewise, the phrase "fast forward" makes more sense after reading "Git moves the pointer forward".

The "Git Branching" chapter is also a place where diagrams really shine.

## Is rebase better than merge

> "(...) rebasing makes for a cleaner history. If you examine the log of a rebased branch, it looks like a linear history."

> "Rebasing replays changes from one line of work onto another in the order they were introduced, whereas merging takes the endpoints and merges them together."

Reading this reminds me of the (newish?) option for merging pull requests on GitHub, ["Rebase and merge your commits"](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges#rebase-and-merge-your-commits) -- as opposed to merge or squash&merge.

## Don't rebase commits and force push to a shared branch

The opportunity to plug another blog post by Julia Evans, ["git rebase: what can go wrong?"](https://jvns.ca/blog/2023/11/06/rebasing-what-can-go-wrong-/).

## The chance of getting the same SHA-1 twice in your repository

> "A higher probability exists that every member of your programming team will be attacked and killed by wolves in unrelated incidents on the same night."

## Ancestry references

The name of references with "^" or "~" (or both!) is "ancestry references". Both `HEAD~3` and `HEAD^^^` are "the first parent of the first parent of the first parent".

## Double dot and triple dot

I do not intend to try and learn this but...

The double-dot syntax "asks Git to resolve a range of commits that are reachable from one commit but aren't reachable from another".

The triple-dot syntax "specifies all the commits that are reachable by either of the two references but not by both of them".

## New-to-me aspects of `git rebase -i`

`git rebase -i` lists commits in the reverse order compared to `git log`. I am not sure why I did not make a note of this before.

I had not really realized one could *edit* single commits in `git rebase -i`. When writing "edit", rebasing will stop at the commit one wants to edit. That strategy is actually featured in the GitHub blog post ["Write Better Commits, Build Better Projects"](https://github.blog/2022-06-30-write-better-commits-build-better-projects/).

## Plumbing vs porcelaine

I had seen these terms before but never taken the time to look them up. *Plumbing commands* are the low-level commands, *porcelain commands* are the more user-friendly commands. At this stage, I do not think I need to be super familiar with plumbing commands, although I did click around in a `.git` folder out of curiosity.

## Removing objects

There is a section in the "Git internals" chapter called "Removing objects". I might come back to it if I ever need to do that... Or I'd use [git obliterate from the git-extras utilities](https://github.com/tj/git-extras/blob/main/Commands.md#git-obliterate)!

## Conclusion

Pro Git was a good read, although I do wish I had bought the second edition. I probably missed good stuff because of this! My next (and last?) Git book purchase will be [Julia Evans' new Git zine](https://jvns.ca/blog/2023/12/31/2023--year-in-review/#moving-slowly-is-okay) when it's finished. I can't wait!

