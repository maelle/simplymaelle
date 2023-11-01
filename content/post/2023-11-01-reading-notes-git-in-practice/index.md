---
title: "Reading notes on Git in Practice by Mike McQuaid"
date: '2023-11-01'
slug: reading-notes-git-in-practice
output: hugodown::hugo_document
tags:
  - good practice
  - books
  - git
rmd_hash: a719d21e47c3e63b

---

While preparing materials for teaching Git a few months ago, I re-read Suzan Baert's excellent [post about Git and GitHub](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/), where she [mentioned](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/#final-thoughts) having read ["Git in Practice" by Mike McQuaid](https://www.manning.com/books/git-in-practice). I added the book to my [Momox](https://en.wikipedia.org/wiki/Momox) alerts, where it got available a few weeks later. The book source is on [GitHub](https://github.com/MikeMcQuaid/GitInPractice#readme).

The book isn't too heavy, so I took it with me on a long train journey! :train:

Here are my reading notes, since I hope this habit of writing book learnings will stick. These notes are of limited generalizability, depending on what your own Git practice and knowledge are. Some chapter numbers are missing: they are those that were less relevant to me.

## First impressions

### Manning Publications book cover

This is the second book published by Manning that I own, the other being ["The Programmer's Brain" by Felienne Hermans](https://www.manning.com/books/the-programmers-brain). Both have covers featuring an ancient drawing of someone wearing a traditional dress, but different drawings of course, and different artists. For "Git in Practice" the cover is "A Kamtchadale in her full dress in 1768". While the editor didn't explain why a particular drawing was chosen, they have a general explanation

> At a time when it is hard to tell one computer book from another, Manning celebrates the inventiveness and initiative of the computer business with book covers based on the right diversity of national costumes (...)

Good to know!

### Not a book for beginners

The book is advertised for users at an "intermediate to advanced level". One thing that clearly shows this is that the content on "Why you should use version control and how to convince others that doing so is a good idea" is an Appendix! Note that the content I'd actually use on the topic is the article ["Excuse me, do you have a moment to talk about version control?" by Jenny Bryan](https://peerj.com/preprints/3159/).

### An older book?

The book was published in 2015. I guess some Git things have been deprecated but I assume most of the book is still valid. What aged really bad: the screenshots from GitHub interface!

### 66 "techniques"

The book cover indicates it includes 66 techniques. The first techniques are `git init`, `git add`, `git commit`: calling them techniques is a bit of a stretch to me? But then the very predictable structure of the book content was actually great, so using that structure from the get go wasn't bad at all.

## Chapter 1: Local Git

### Clearer understanding of Git add

> "Rather than require all changes in the working tree to make up the next commit, Git allows files to be added incrementally to the index"

So, while I've been practicing this, I'm not sure I ever expressed the usefulness of `git add`.

I also underlined a sentence explaining that `git add` does two things:

> "git add is used both to initially add a file to the Git repository and to request that changes to the fine be used in the next commit."

### Garbage in, garbage out

I liked that sentence:

> "How useful the history is relies a great deal on the quality of data entered into it."

Then the author mentions two reasons why small commits are better:

-   Better readability of the history;
-   Easier reversal of an unwanted change.

### Commit messages are structured like emails

And apparently this comes from the history of Git, where commits could be sent as email. So the first line of a commit message is the subject and the rest, the body.

I guess there's also a parallel between the [conventional commits convention](https://www.conventionalcommits.org/en/v1.0.0/), where you add a prefix to the subject indicating its category: "fix:" for bug fixes, "feat:" for features, is like adding a prefix to an email subject: "\[ACTION REQUIRED\]".

### A motivation for rewriting history

> "there's no need for everyone to see the mistakes you made along the way"

Now let's see if this, and other parts of the book, motivate me enough to rewrite the history of my Git branches before I mark a PR as ready for review.

### A better understanding of Git diff

I didn't know that one could call `git diff` without an argument, and that it'd show the diff between the working directory and the index staging area... but apparently only for files that were already added, which answers my question about how different that is from `git status`:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>git_repo</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempdir</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_repo.html'>git_init</a></span><span class='o'>(</span><span class='nv'>git_repo</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"blop"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"bla.txt"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;      file status staged</span></span>
<span><span class='c'>#&gt; 1 bla.txt    new   TRUE</span></span>
<span></span><span><span class='nv'>first_commit</span> <span class='o'>&lt;-</span> <span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"first commit"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"bip"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"other.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_diff.html'>git_diff</a></span><span class='o'>(</span><span class='s'>"HEAD"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;   status     old     new</span></span>
<span><span class='c'>#&gt; 1      A bla.txt bla.txt</span></span>
<span><span class='c'>#&gt;                                                                                                                                patch</span></span>
<span><span class='c'>#&gt; 1 diff --git a/bla.txt b/bla.txt\nnew file mode 100644\nindex 0000000..35733a0\n--- /dev/null\n+++ b/bla.txt\n@@ -0,0 +1 @@\n+blop\n</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_diff.html'>git_diff</a></span><span class='o'>(</span><span class='nv'>first_commit</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;   status     old     new</span></span>
<span><span class='c'>#&gt; 1      A bla.txt bla.txt</span></span>
<span><span class='c'>#&gt;                                                                                                                                patch</span></span>
<span><span class='c'>#&gt; 1 diff --git a/bla.txt b/bla.txt\nnew file mode 100644\nindex 0000000..35733a0\n--- /dev/null\n+++ b/bla.txt\n@@ -0,0 +1 @@\n+blop\n</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_diff.html'>git_diff</a></span><span class='o'>(</span>repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] status old    new    patch </span></span>
<span><span class='c'>#&gt; &lt;0 rows&gt; (or 0-length row.names)</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_status</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;                                                              file  status</span></span>
<span><span class='c'>#&gt; 2  content/post/2023-01-02-reading-notes-git-in-practice/index.md deleted</span></span>
<span><span class='c'>#&gt; 1 content/post/2023-01-02-reading-notes-git-in-practice/index.Rmd deleted</span></span>
<span><span class='c'>#&gt; 3          content/post/2023-11-01-reading-notes-git-in-practice/     new</span></span>
<span><span class='c'>#&gt;   staged</span></span>
<span><span class='c'>#&gt; 2  FALSE</span></span>
<span><span class='c'>#&gt; 1  FALSE</span></span>
<span><span class='c'>#&gt; 3  FALSE</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"bip"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_diff.html'>git_diff</a></span><span class='o'>(</span>repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;   status     old     new</span></span>
<span><span class='c'>#&gt; 1      M bla.txt bla.txt</span></span>
<span><span class='c'>#&gt;                                                                                                                     patch</span></span>
<span><span class='c'>#&gt; 1 diff --git a/bla.txt b/bla.txt\nindex 35733a0..94652b8 100644\n--- a/bla.txt\n+++ b/bla.txt\n@@ -1 +1 @@\n-blop\n+bip\n</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_status</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;                                                              file  status</span></span>
<span><span class='c'>#&gt; 2  content/post/2023-01-02-reading-notes-git-in-practice/index.md deleted</span></span>
<span><span class='c'>#&gt; 1 content/post/2023-01-02-reading-notes-git-in-practice/index.Rmd deleted</span></span>
<span><span class='c'>#&gt; 3          content/post/2023-11-01-reading-notes-git-in-practice/     new</span></span>
<span><span class='c'>#&gt;   staged</span></span>
<span><span class='c'>#&gt; 2  FALSE</span></span>
<span><span class='c'>#&gt; 1  FALSE</span></span>
<span><span class='c'>#&gt; 3  FALSE</span></span>
<span></span></code></pre>

</div>

### Smiling refs

I learnt that `main^` is equivalent to `main~1` and `main^^` is equivalent to `main~2`. ^\_^

### Git rev-parse

So if you use say `main^^` and want to get the actual SHA, you can use `git rev-parse`. In gert, that's [gert::git_commit_id()](https://github.com/r-lib/gert/issues/70).

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>git_repo</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempdir</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_repo.html'>git_init</a></span><span class='o'>(</span><span class='nv'>git_repo</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"blop"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"bla.txt"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;      file status staged</span></span>
<span><span class='c'>#&gt; 1 bla.txt    new   TRUE</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"first commit"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "c17a89d5682d3bab91e412aa935062c35dcdc1e2"</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"blip"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"bla.txt"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;      file   status staged</span></span>
<span><span class='c'>#&gt; 1 bla.txt modified   TRUE</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"second commit"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "9a02ae19cbcd1f03bd68aba9cd414b7d83250d3d"</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"blup"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"bla.txt"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;      file   status staged</span></span>
<span><span class='c'>#&gt; 1 bla.txt modified   TRUE</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"third commit"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "cd437aebe6e3f11223cdc4c4d62e3660bde56dbd"</span></span>
<span></span><span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit_id</a></span><span class='o'>(</span><span class='s'>"main"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "cd437aebe6e3f11223cdc4c4d62e3660bde56dbd"</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit_id</a></span><span class='o'>(</span><span class='s'>"main^"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "9a02ae19cbcd1f03bd68aba9cd414b7d83250d3d"</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit_id</a></span><span class='o'>(</span><span class='s'>"main^^"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "c17a89d5682d3bab91e412aa935062c35dcdc1e2"</span></span>
<span></span></code></pre>

</div>

## Chapter 2: Remote Git

Interestingly that's the chapter where branches are introduced.

### Git as a distributed version control system

My first version control experience was with SVN, for a package developed on R-forge. I only remember two things: the cute tortoise of Tortoise-SVN, and the fact that instead of doing commit+push I'd only do commit. It makes sense now that I understand that Git is a distributed version control system whereas SVN is centralized, with a remote repo being the source of truth.

### Why Git fetch?

To be honest, I'm still not sure when I would use `git fetch`! `git fetch` is for fetching changes from a remote without modifying local branches. I only use `git pull` (which is fetch+merge) or `git pull --rebase` when that fails, mostly. Note that the ["Pull, but you have local work" chapter](https://happygitwithr.com/pull-tricky.html) of happygitwithr by Jenny Bryan, the STAT 545 TAs, Jim Hester looks useful (I found it by searching for "fetch" in that book).

Mike McQuaid wrote a paragraph "Should you use pull or fetch?" where he explains he prefers git fetch because he can incorporate remote changes when it makes sense, using a method of his choice (merging, rebasing, resetting). He also says `git fetch` is better for working in situations with no internet connection, which was unclear to me.

### Branch names

Mike McQuaid recommends creating a branch name that describes "the branch's purpose in multiple words separated by hyphens". That's what I tend to do, probably imitating what I saw elsewhere. Sometimes my branch names are less informative, and this makes me cringe when I open the corresponding PR.

### Pushing a local branch remotely

This section just made me very happy I push local branches with [`gert::git_push()`](https://docs.ropensci.org/gert/reference/git_fetch.html), because it sets the upstream branch automatically by default.

### Slowly, slowly understanding merge commits

I learnt that merge commits are commits with multiple parents when working on the [fledge package](https://fledge.cynkra.com/). When reading "Git in practice", I at least understood "fast-forward merge" which is something I had simply ignored when Git told me it had done one.

So, for my future reference... If I am merging a branch A into main, and Git tells me it did a fast-forward merge, it means no merge commit was needed so none was made. The branch A commits were added to the main branch without new commit before that.

### Conflicts and merge commits

> "When conflicts have been resolved, a *merge commit* can be made. This stores the two parent commits and the conflicts that were resolved so they can be inspected un the future. Unfortunately, sometimes people pick the wrong option or merge incorrectly, so it's good to be able to later see what conflicts they had to resolve."

This was new to me, certaintly not something I had in mind when resolving conflicts.

## Chapter 3: Filesystem interactions

### Git mv, Git rm

I was never aware of `git mv`, for renaming and moving a file. I think it's because the RStudio IDE Git thingie was doing this for me? When I rename a file, I see the status becomes "R" once I've added both the file deletion (old name) and the file adding (new name).

The same goes for `git rm` for removing a file.

### I should use Git reset more often

An use case for `git reset` that was described really resonated with me. :sweat_smile:

> "Perhaps you added debugging statements to files and have now commited a fix, so you want to reset all the files that haven't been committed to their last committed state (on the current branch)".

So instead of looking at the Git diff to remember where I added [`browser()`](https://rdrr.io/r/base/browser.html), or looking for occurrences of a given debugging thing, I could use `git reset`, now that sounds smarter!

### I should adopt Git clean

`git clean` will remove any untracked file from the filesystem. Like the previous idea, it sounds like something I could use when debugging something and creating files à la "blop.text".

Now `git clean` can be dangerous so you can first run `git clean -n` or `git clean --dry-run` to see what would be removed.

Note that gert doesn't seem to support `git clean` so I'd do this from a terminal.

The book also explains the `-X` argument of `git clean` for removing only ignored files, compared to the lower case `-x` argument for removing both ignored and untracked files.

In conclusion I need to practice using `git clean`, and to refer to its docs when doing so, but at least now I know it exists.

### Could Git stash be useful to me

`git stash save` is a command for temporarily stashing some changes. `git stash pop` gets them back. `git stadsh clear` clears stashed commits.

I can't think of an use case right now but maybe I will encounter one one day?

### Git update-index `--assume-unchanged` <path>

Again an use case that resonated with me (well I don't use Rails but still)

> "I've found myself in a situation in the past where I wanted to test a Rails configuration file change for a week or two while continuing to do my normal work. I didn't want to commit it because I didn't want it to apply to servers or my coworkers, but I did want to continue testing it while I made other commits rather than change to a particular branch each time."

So in that case one can run `Git update-index --assume-unchanged <path>`. Then you undo this by running `Git update-index --no-assume-unchanged <path>`.

## Chapter 4: History visualization

Honestly most of this chapter made me happy about the GitHub interface!

The other thing is that it has an explanation of `git bisect` that I need to come back to next time I need to find which commit caused a particular bug. I tend to be scared of `git bisect`, but the explanation made sense. Also I guess I need to focus on making small informative commits before bisect really shines in my use cases. :innocent:

I am especially curious about `git bisect run` for automating `git bisect`, I wonder how that would work with a testthat unit test.

## Chapter 5: Advanced branching

### Merge strategies

I learnt there's a merge strategy, `--ours`, that will ignore all changes from the incoming branch but still indicate it in the history. That's supposed to be useful for when you want to keep an experiment from an experimental branch in the history.

There's another interesting merge strategy, `--patience`, that "uses a slightly more expensive git diff algorithm to try to decrease the chance of a merge conflict". It sounds like a joke.

### Not useful to me but fun name: git rerere

`git rerere`, Reuse Recorded Resolution, allows you to resolve each merge conflict only once.

I sort of understand the use case but it doesn't sound useful to my work:

> "You may find yourself in a situation where you have a long-running branch that you have to keep merging in another branch, and you get the same merge conflict every time."

### Yay git cherry-pick

`git cherry-pick` allows you to add a single commit to the current branch. I recently successfully used it!

A collaborator had mistakenly created a new feature branch from another feature branch instead of from the main branch. After that they added their feature commit but the PR was all broken. I created a new branch from the main branch, cherry-picked the commit and then opened a new PR. It's a small step for humanity but it was a big step for me. :grin: It was very cool to be able to say "don't worry, I've got it" to that collaborator.

## Chapter 6: Rewriting history and disaster recovery

### Another new-to-me command: git reflog

`git reflog` will list "all changes including history rewrites", for the local repository. Commits that are deleted on all branches are only kept in the reflog for 90 days.

### Git reset can take a list of paths as arguments

An example would be `git reset HEAD^ -- path.txt`

### Actually retired now: Git filter-branch

One technique presented in the book, for rewriting git repository history, is `git filter-branch` but then I saw a [toot by Kirill Müller](https://mastodon.social/deck/@kirill@fosstodon.org/111302703034532823) indicating it's retired. A replacement is [git filter-repo](https://github.com/newren/git-filter-repo/), that I'd have to explore a bit more before being able to use it. The [simple example](https://github.com/newren/git-filter-repo/#simple-example-with-comparisons) looks convincing enough! According to the book this can be useful for some cases of disaster recovery.

### Commit regularly

This is the main message of this chapter, and it's not an advanced technique at all, just a good habit. :smile_cat:

## Chapter 7: Personalizing Git

### Option for pruning branches automatically

If you want the references to now deleted remote branches to be deleted, you can run `git remote prune origin` or configure Git so that this will happen every time you pull or fetch.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_config.html'>git_config_global_set</a></span><span class='o'>(</span><span class='s'>"fetch.prune"</span>, <span class='m'>1</span><span class='o'>)</span></span></code></pre>

</div>

### Global gitignore

I already knew about the global gitignore file thanks to the handy [`usethis::git_vaccinate()`](https://usethis.r-lib.org/reference/git_vaccinate.html).

### An option for autocorrecting misspelled commands

There's a Git option for autocorrecting misspelled commands, [help.autocorrect](https://git-scm.com/book/en/v2/Customizing-Git-Git-Configuration#_help_autocorrect). It would not help me since my most frequent typo is "gut" instead of "git", sadly.

## Chapter 8: Vendoring dependencies as submodules

If I ever have to work again with a repo that uses submodules :sob:, I might go back to this chapter to see which commands to run.

## Chapter 12: Creating a clean history

### One can build a commit from parts of files

Using `git add --patch <path>`. This opens an interactive menu letting you choose which hunk(s) to add to a commit. Each hunk can be split into smaller hunks. Then one uses `git commit` as usual.

The book also mentions that `git commit --patch` also exists. It seems less intuitive to me to do the hunk selection at that stage.

## Chapter 13: merging vs. rebasing

This chapter compares the strategies of two projects:

-   CMake's branching and merging strategy.
-   Homebrew's rebasing and squashing strategy. "This hides merge commits, evidence of branches, and temporary commits (for example, those that fix previous commits on the same branch) from the main branch."

To me CMake history looks like a mess or even a nightmare so I guess I'm in favor of the Homebrew's strategy!

But in each project, it is worth to have a discussion about the chosen strategy, to educate contributors.

I liked reading that in Homebrew, metadata is included into commit messages. For instance I enjoy the fact that GitHub automatically adds co-authors when you squash and merge a PR that add multiple contributors: <https://docs.github.com/en/pull-requests/committing-changes-to-your-project/creating-and-editing-commits/creating-a-commit-with-multiple-authors>

## Chapter 14: Recommended team workflows

Have you heard of GitHub flow, Git flow? These are *team workflows*.

> "The different strategies for deciding how and when to branch, merge or rebase as part of a team are called *team workflows*."

I much prefer GitHub flow where the default branch is stable and where there is no long-lived development branch, because in my experience, having such a long-lived development branch has lead to more confusion than gains. For [rOpenSci dev guide](https://docs.github.com/en/pull-requests/committing-changes-to-your-project/creating-and-editing-commits/creating-a-commit-with-multiple-authors) we went from some sort of Git flow to GitHub flow, where actual relases will be, well, actual releases/tags of the main branch. Changing flows was such a relief!

The author also presented his own two usual flows.

## Conclusion

"Git in practice" by Mike McQuaid was a nice train travel companion. I feared it would be too dry or obscure but found it clear and well explained. I learnt a lot, but now, I will have to... practice what I learnt! :grin:

Did you read this book, or another Git book? How did you learn Git? Any strong opinion on any of the topics mentioned in those notes?

