---
title: "Hack your way to a good Git history"
date: '2024-06-11'
slug: rewrite-git-history
output: hugodown::hugo_document
tags:
  - good practice
  - git
rmd_hash: 064686e26fd02e4f

---

I've now explained on this blog why it's important to have [small, informative Git commits](/2024/06/03/small-commits/)[^1] and how I've realized that polishing history can happen in a [second phase of work in a branch](/2023/12/07/two-phases-git-branches/). However, I've more or less glossed over *how* to craft the history in a branch once you're done with the work.

I've entitled this post *"Hack your way to a good Git history"* because writing the history after the fact can feel like cheating, but it's not!

## To branch or not to branch

With the second method of re-writing history I'll present, you could get away with working on the default branch as long as you do not push. However, I'd really recommend creating a branch before you start working on whatever task you've assigned yourself, be it a bug fix, new feature or some refactoring. It feels safer, and it's a good habit to take.

On the default branch, you can't force push, whereas in your own branch, you can push regularly as your work, as a backup of sorts, then rewrite history, then force push.

Now let's assume you did a bunch of changes in a branch: adding a bug fix to a script, as well as refactoring part of it, adding a test to ensure the bug never reappears, updating the changelog[^2]. You have a dozen of commits because actually fixing the commit took more than one try, and you first got your test wrong so it took you 3 commits.

## "Squash and merge": click the right GitHub/GitLab button

If you're fine with all your changes being reduced to a single commit, you can *squash* merge all the changes from your branch into a single commit on the default branch.

`--squash` is an option of [`git merge`](https://git-scm.com/docs/git-merge) but to be honest, I always merge branches through the GitHub pull requests interface. You can easily perform a "squash and merge" on [GitHub](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges#squash-and-merge-your-commits) and [GitLab](https://docs.gitlab.com/ee/user/project/merge_requests/squash_and_merge.html).

It's really the easiest method to get a good Git history that does not include your desperate intermediary steps, but it does not allow for more granularity than one commit. It also means that if someone reviews your pull request, they do it without the benefit of a story in the form of an informative succession of Git commits.

## "The repeated amend":tm:: `git commit --amend`

*Thanks to Jenny Bryan for describing this workflow and for [pointing it to me](https://fosstodon.org/@jennybryan/111540760887095683).*

This second method comes from the excellent [Happy Git and GitHub for the useR by Jenny Bryan](https://happygitwithr.com/repeated-amend). The idea is than when fixing a bug in your script for instance, you make a first change, create a commit with the message "fix: fix indentation bug" or so, then do more changes and *amend the commit* with `git commit --amend` instead of creating a new one.

This way, you 'build up a "good" commit gradually'.

You can use this method on the default branch, if you don't push before the commit is really done. If you use this method on a branch of yours, you can force push. I really enjoy this method, when writing blog posts for instance!

This method does allow for granularity, as you can first "repeat amend" a refactoring commit, then "repeat amend" a bug fix commit, then "repeat amend" a new test commit, etc.

You can practice `git commit --amend` with those exercises of the saperlipopette package: ["Oh shit, I committed and immediately realized I need to make one small change!"](https://docs.ropensci.org/saperlipopette/reference/exo_one_small_change.html) and ["Oh shit, I need to change the message on my last commit!"](https://docs.ropensci.org/saperlipopette/reference/exo_latest_message.html).

## "Start from scratch": `git reset --soft` + `git add` (`--patch`)

*Thanks to [Hugo Gruson](https://hugogruson.fr/) for introducing me to this workflow.*

So, you have all the changes in three places (R script, test file, `NEWS.md`) and a dozen commits, let's say 12.

With this method you first reset the Git history to how it was before it started, without removing the changes to the files: `git reset --soft HEAD~12`. Now you will gradually create the 4 commits you want:

-   "refactor: stop using useless comments"
-   "fix: fix indentation bug"
-   "test: add test for indentation bug"
-   "docs: update changelog"

For the first commit, you run `git add --patch R/script.R` to only pick the refactoring changes you made to the script, then `git commit -m "refactor: stop using useless comments"`. For the other commits, you should be able to use `git add` without the patch option.

Then, you force push to your branch (not the default branch!).

You can practice `git add --patch` with this exercise of the saperlipopette package: ["Hey I'd like to split these changes to the same file into several commits!"](https://docs.ropensci.org/saperlipopette/reference/exo_split_changes.html).

## "Mix and match your commits": `git rebase -i`

With this method, instead of throwing away the commits, you will be able to combine them into a commit (squash), change their message (reword), edit them (edit), delete them, change their order.

Say you start from

    fix: fix indentation bug
    try again
    ok like this?!!
    pff so hard
    found the actual bug!
    ah no
    found it
    refactor: stop using useless comments
    test: add test for indentation bug
    oops, forgot a quote
    actually follow our own standards
    docs: update changelog

`git rebase -i` will get you to something like:

    pick <hash> fix: fix indentation bug
    pick <hash> try again
    pick <hash> ok like this?!!
    pick <hash> pff so hard
    pick <hash> found the actual bug!
    pick <hash> ah no
    pick <hash> found it
    pick <hash> refactor: stop using useless comments
    pick <hash> test: add test for indentation bug
    pick <hash> oops, forgot a quote
    pick <hash> actually follow our own standards
    pick <hash> docs: update changelog

That you edit to:

    pick <hash> refactor: stop using useless comments
    pick <hash> fix: fix indentation bug
    squash <hash> try again
    squash <hash> ok like this?!!
    squash <hash> pff so hard
    squash <hash> found the actual bug!
    squash <hash> ah no
    squash <hash> found it
    pick <hash> test: add test for indentation bug
    squash <hash> oops, forgot a quote
    squash <hash> actually follow our own standards
    pick <hash> docs: update changelog

to get your history to:

    refactor: stop using useless comments
    fix: fix indentation bug
    test: add test for indentation bug
    docs: update changelog

I wrote `git rebase -i` but it actually needs an argument: the commit from which to rebase. Usually I run

    git merge-base my-branch main

to get that commit and copy-paste it to my next command. In theory I could *count* commits and use something à la `HEAD~n` but for some reason it makes me more nervous.

After that, you can force push to your branch.

You can practice `git rebase -i` with this exercise of the saperlipopette package: ["Hey I'd like to make my commits in a branch look informative and smart!"](https://docs.ropensci.org/saperlipopette/reference/exo_rebase_i.html).

Beside practicing, a really important thing is to configure your Git editor. I do not remember how I did it but now my Git editor is Atom, which I find easier to deal with than Vim[^3].

Do not miss [Julia Evans' rules for rebasing](https://wizardzines.com/comics/rules-for-rebasing/). One of them is to not do more than one thing (for instance not reordering and squashing like we did above :sweat_smile:), you can instead do several `git rebase -i` in a row!

## How to merge if you have \>1 nice commits

If your branch features more than one nice commits and you don't squash it, then, you can [rebase and merge it](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/about-pull-request-merges#rebase-and-merge-your-commits) so that no merge commits is created. This way, your default branch has a nice readable history.

## Conclusion

In this post, I explained everything I know about creating a good Git history. Please do not look at the commits I end up pushing to default branches, I'm still learning. :grin: I'm doing my best to rewrite history as often as I can, to follow Martin Fowler's principle ["if it hurts, do it more often"](https://martinfowler.com/bliki/FrequencyReducesDifficulty.html).

[^1]: I should also have added "atomic", because if you can't load your project at a given commit because of a missing parenthesis, then you can't run Git bisect for instance.

[^2]: If you find updating the changelog manually is a bit of a faff, check out the [fledge package](https://fledge.cynkra.com/dev/).

[^3]: Although I suppose I could one day try and get better at Vim... Baby steps...

