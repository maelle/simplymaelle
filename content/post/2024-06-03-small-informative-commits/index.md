---
title: "Why you need small, informative Git commits"
date: '2024-06-03'
slug: small-commits
output: hugodown::hugo_document
tags:
  - good practice
  - git
rmd_hash: fff563e51971c127

---

"Make small Git commits with informative messages" is a piece of advice we hear a lot when learning Git. That's why we might want to sometimes [rewrite history in a branch](/2023/12/07/two-phases-git-branches/). In this post, I'd like to underline three main (:wink:) reasons why you'll be happy you, or someone else, made small and informative Git commits in a codebase.

A disclaimer: these three reasons are only valid if you do not write perfectly working and readable code all the time. If you do, you won't need to use your Git log for debugging and undoing purposes, so who cares about your Git commits?

## A mysterious line of code (Git blame)

Imagine you encounter a mysterious line of code in a script that either someone else or yourself wrote a while ago.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nv'>x</span> <span class='o'>-</span> <span class='m'>1</span></span></code></pre>

</div>

{{< figure src="mystery.png" alt="An R script with a line 'x <- x - 1'" width=400 >}}

As luck will have it, there is no comment explaining the line around it. How can you guess the intent of that line?

A Git command might help you: Git blame! It'd better called Git explain, as blaming is a bad idea. Anyway, it's a tool for seeing the code with more context. For each line, you get to see when it was added or last modified, by whom, and when.

{{< figure src="blame.png" width=500 alt="simplified diagram of Git blame: for each line in a script on the left we see who added it, when, with what commit message.">}}

For instance, I added or modified the 2 lines at the top and the the two lines in the middle, that are highlighted in yellow, a few years ago. The line that is interesting to me, 'x \<- x - 1', was added by ropenscibot in 2017.

Now, I can try and display more information about the Git commit that last modified, or added that mysterious line. The person who last touch the line chose our adventure...

-   If we're unlucky, we get a Git commit whose message is '"Commit a bunch of files before lunch :spaghetti:"' and whose diff shows 145 changed files with 2,624 additions and 2,209 deletions. Not exactly helpful, we're still stuck.
-   If we're lucky, we get a Git commit whose message is '"fix: adapt code to tool's 0-indexing"' and whose diff shows 2 changed files with 3 additions and 2 deletions. Now we know why x was modified this way! We can add an explaining [comment](https://blog.r-hub.io/2023/01/26/code-comments-self-explaining-code/) to make the code more readable for later, and continue with our work.

Git blame is a built-in Git thing, but for me, it's first and foremost a [button](https://docs.github.com/en/repositories/working-with-files/using-files/viewing-a-file#viewing-the-line-by-line-revision-history-for-a-file) in the Github interface for any file. Use Git blame as you prefer.

## A bad idea 7 commits ago (Git revert)

Oh no, that idea from 7 commits ago is bad! Do we

-   Manually remove the change;

-   Simply revert the commit that added the change?

Once again, the quality and granularity of the commits will decide for you. If the snapshot from 7 commits ago included many different unrelated changes, it will be easier to manually undo the changes yourself by going into the files and deleting or amending stuff. If the snapshot from 7 commits ago was small enough, you can use Git revert with that commit ID, that will create a new commit undoing that commit.

You can try out Git revert in an [exercise of the saperlipopette package](https://maelle.github.io/saperlipopette/reference/exo_revert_file.html).

## All was well 3 days ago, now my thing is broken

Imagine you were doing something with your somewhat complex R project 3 days ago, and that all was well.

{{< figure src="cool.png" width=200 alt="a bunch of scripts, on them an emoji of a happy face wearing sunglasses.">}}

Now, after a bunch of changes to your still complex R project, you set out to do the same thing, and... it no longer works.

{{< figure src="sob.png" width=200 alt="a bunch of scripts, on them an emoji of a sobbing face.">}}

To add insult to injury, your usual [debugging](https://www.pipinghotdata.com/talks/2022-11-11-debugging/) tools aren't helping you for some reasons. Is all hope lost? No!

Git has a tool to help you go over the Git history between now and then (3 days ago, 100 commits ago) in an optimal way: Git bisect. Git bisect will make you try some commits to help pinpoint which change, which commit exactly, introduced the bug you're now observing.

Your Git history looks something like this:

-   a commit at which you know the thing worked,
-   a commit at which it no longer works,
-   with commits in between about which you have no information because you were not doing or testing the thing, so at some point you introduced a bug.

{{< figure src="graph.png" width=700 alt="a series of emoji, the first one a cool face, then sleeping faces, then a sobbing face.">}}

You tell Git bisect which commit was for sure good, and which commit is bad, and it starts putting your project in the state it was at a given commit, let's say the third commit:

{{< figure src="commit1.png" width=700 alt="arrow above a sleeping face">}}

You have to tell Git bisect whether that commit is good or bad (no complex vocabulary for once). Let's say your thing seems to work at that point.

{{< figure src="commit1good.png" width=700 alt="all faces until the arrow are now cool faces">}}

Git bisect will assume all commits up to that commit were good.

It now makes you explore another commit.

{{< figure src="commit2.png" width=700 alt="arrow above a different sleeping face">}}

You try your thing, it does not work. Therefore this commit, and the following ones, are bad.

{{< figure src="commit2bad.png" width=700 alt="all faces after the arrow are now sobbing">}}

You're in luck, after only a few steps you get to the last commit to try out.

{{< figure src="commit3.png" width=700 alt="arrow on last sleeping face">}}

Again a bad one, your thing does not work for your project in that state.

{{< figure src="commit3bad.png" width=700 alt="last sleeping face now sobbing">}}

It's the first bad commit, so it's the one that introduced the breaking change!

Now, again, the person who made that commit chose our adventure:

-   If we're unlucky, we get a Git commit whose message is '"Commit a bunch of files before workout :muscle:"' and whose diff shows 145 changed files with 2,624 additions and 2,209 deletions. Nooo, that's still a ton of code to read.
-   If we're lucky, we get a Git commit whose message is '"refactor: start using YAML"' and whose diff shows 2 changed files with 3 additions and 2 deletions. Ah, YAML, what an idea! We can now search where we forgot to add quotes in a YAML file. :wink:

You can practice Git bisect in an [exercise of the saperlipopette package](https://maelle.github.io/saperlipopette/reference/exo_bisect.html).

## Two bonus reasons

### Easier review

Small commits will be easier to review by another human. Ideally, it means the code that ends up in the codebase is better, so that there'll be less future need of debugging and undoing. :grin:

### Better automatic changelog generation

If you use a tool like the R package [fledge](https://fledge.cynkra.com/dev/) for generating your changelog or changelog draft, that uses Git commits as input data... you'll get a better starting changelog if your commits are good.

## Conclusion

In this post I showed three main reasons for making small and informative Git commits, all related to debugging or undoing some bad changes. So, creating small, informative Git commits will help someone in the future in trying times. I hope it can inspire myself in particular to pay attention to the Git history I create. :grin:

The saperlipopette package contains a few [exercises](https://maelle.github.io/saperlipopette/reference/index.html) that can help write a better Git history, including: ["Oh shit, I committed and immediately realized I need to make one small change!"](https://maelle.github.io/saperlipopette/reference/exo_one_small_change.html), ["Hey I'd like to split these changes to the same file into several commits!"](https://maelle.github.io/saperlipopette/reference/exo_split_changes.html), ["Hey I'd like to make my commits in a branch look informative and smart!"](https://maelle.github.io/saperlipopette/reference/exo_rebase_i.html)

