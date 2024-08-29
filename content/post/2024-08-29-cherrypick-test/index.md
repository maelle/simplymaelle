---
title: "Does my test really validate a bug fix? Check it with git cherry-pick"
date: '2024-08-29'
slug: cherrypick-test
output: hugodown::hugo_document
tags:
  - package development
  - git
rmd_hash: d7e2fe747239ec41

---

Earlier this year I wrote a post about [git worktree](/2024/01/23/git-worktree/) that allows you to load different versions of an R package at once on your computer. To keep with the "juggle between versions of a codebase with Git plant-related commands" theme, let me show you how I use cherry-pick to assess the quality of an unit test.

## Scenario: you fix a bug in a branch

In a perfect world, the bug you're working on is paired with a :sparkles: [reprex](https://reprex.tidyverse.org/) :sparkles:, and you even start your work by adding a test case for it. The test case fails at first, then you fix the bug in the code so that the test case succeeds.

You might even push the test in a first commit, so that on continuous integration you get a failure then a success in later commits on the branch. Note that merging the commits in this order without [rewriting history](/2024/06/11/rewrite-git-history/) means that [debugging with git bisect](/2024/06/03/small-commits/#all-was-well-3-days-ago-now-my-thing-is-broken) later on might be problematic, as the codebase at the commit that added the test had, well, failing tests. Thanks [Kirill](https://github.com/krlmlr/) for that useful reminder.

## Problem: you add a test after the fix, not before the fix

Now, sometimes things are less than optimal and you add the test case after the fact. So you have a commit or several ones fixing the bug, then a commit adding a passing test. Maybe there was not even a reprex so you're not really sure what the same code snippet returned before the bug fix. How can you ensure the new test was failing before you added the fix?

Without undoing the bug fix using code comments, that is. :wink:

## Solution: a throwaway branch and git cherry-pick

What I've done a few times now (so clearly an expert workflow :sweat_smile:) is

-   getting the hash of the commit that added the test.

-   returning to the main branch, for instance with `gert::git_checkout("main")`.

-   creating a throwaway branch, for instance with `gert::git_branch_create("deleteme")`.

-   putting the test on that branch with git cherry-pick, for instance with `gert::git_cherry_pick("<commit-hash>")`.

-   running the test. It is so validating when it does fail on the main branch! It's a good test! :tada:

-   returning to the main (or the bug-fix) branch, for instance with `gert::git_checkout("main")` (or `gert::git_checkout("bug-fix")`).

-   deleting the throwaway branch, for instance with `gert::git_branch_delete("deleteme")`. :innocent:

## Conclusion

In this post I've explained a small git workflow I enjoy for checking a test of a bug fix is a good test for it: failing without the bug fix, succeeding with the bug fix. I find this process a nice use case for cherry-pick, beside [moving a commit that was made on the wrong branch](https://maelle.github.io/saperlipopette/reference/exo_committed_to_wrong.html).

