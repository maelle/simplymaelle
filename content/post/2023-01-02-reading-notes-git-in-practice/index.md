---
title: "Reading notes on Git in Practice by Mike McQuaid"
date: '2023-11-02'
slug: reading-notes-git-in-practice
output: hugodown::hugo_document
tags:
  - good practice
  - books
  - git
rmd_hash: 4540565d08c22e78

---

While preparing materials for teaching Git a few months ago, I re-read Suzan Baert's excellent [post about Git and GitHub](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/), where she [mentioned](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/#final-thoughts) having read ["Git in Practice" by Mike McQuaid](https://www.manning.com/books/git-in-practice). I added the book to my [Momox](https://en.wikipedia.org/wiki/Momox) alerts, where it got available a few weeks later. Here are my reading notes, since I hope this habit of writing book learnings will stick. These notes are of limited generalizability, depending on what your own Git practice and knowledge are.

## First impressions

### Manning Publications book cover

This is the second book published by Manning that I own, the other being "The Programmer's Brain" by Felienne Hermans. Both have covers featuring an ancient drawing of someone wearing a traditional dress, but different drawings of course, and different artists. For "Git in Practice" the cover is "A Kamtchadale in her full dress in 1768". While the editor didn't explain why a particular drawing was chosen, they have a general explanation

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

I guess there's also a parallel between conventional commits convention, where you add a prefix to the subject indicating its category: "fix:" for bug fixes, "feat:" for features, is like adding a prefix to an email subject: "\[ACTION REQUIRED\]".

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
<span><span class='c'>#&gt;                                                              file   status</span></span>
<span><span class='c'>#&gt; 2  content/post/2023-01-02-reading-notes-git-in-practice/index.md      new</span></span>
<span><span class='c'>#&gt; 1 content/post/2023-01-02-reading-notes-git-in-practice/index.Rmd modified</span></span>
<span><span class='c'>#&gt;   staged</span></span>
<span><span class='c'>#&gt; 2  FALSE</span></span>
<span><span class='c'>#&gt; 1  FALSE</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"bip"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_diff.html'>git_diff</a></span><span class='o'>(</span>repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;   status     old     new</span></span>
<span><span class='c'>#&gt; 1      M bla.txt bla.txt</span></span>
<span><span class='c'>#&gt;                                                                                                                     patch</span></span>
<span><span class='c'>#&gt; 1 diff --git a/bla.txt b/bla.txt\nindex 35733a0..94652b8 100644\n--- a/bla.txt\n+++ b/bla.txt\n@@ -1 +1 @@\n-blop\n+bip\n</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_status</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;                                                              file   status</span></span>
<span><span class='c'>#&gt; 2  content/post/2023-01-02-reading-notes-git-in-practice/index.md      new</span></span>
<span><span class='c'>#&gt; 1 content/post/2023-01-02-reading-notes-git-in-practice/index.Rmd modified</span></span>
<span><span class='c'>#&gt;   staged</span></span>
<span><span class='c'>#&gt; 2  FALSE</span></span>
<span><span class='c'>#&gt; 1  FALSE</span></span>
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
<span><span class='c'>#&gt; [1] "a4c57c453adabe9f44706bb861e2e72b66fad8f8"</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"blip"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"bla.txt"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;      file   status staged</span></span>
<span><span class='c'>#&gt; 1 bla.txt modified   TRUE</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"second commit"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "9cf1a500b5096725624be0e5b6d7cdf35edbfba6"</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='s'>"blup"</span>, path <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>git_repo</span>, <span class='s'>"bla.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"bla.txt"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;      file   status staged</span></span>
<span><span class='c'>#&gt; 1 bla.txt modified   TRUE</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"third commit"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "4e1b1b6264d804a72c91d0289b780117168fac9b"</span></span>
<span></span><span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit_id</a></span><span class='o'>(</span><span class='s'>"main"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "4e1b1b6264d804a72c91d0289b780117168fac9b"</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit_id</a></span><span class='o'>(</span><span class='s'>"main^"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "9cf1a500b5096725624be0e5b6d7cdf35edbfba6"</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit_id</a></span><span class='o'>(</span><span class='s'>"main^^"</span>, repo <span class='o'>=</span> <span class='nv'>git_repo</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "a4c57c453adabe9f44706bb861e2e72b66fad8f8"</span></span>
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

I learnt that merge commits are commits with multiple parents when working on the fledge package. When reading "Git in practice", I at least understood "fast-forward merge" which is something I had simply ignored when Git told me it had done one.

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

### Git update-index --assume-unchanged <path>

Again an use case that resonated with me (well I don't use Rails but still)

> "I've found myself in a situation in the past where I wanted to test a Rails configuration file change for a week or two while continuing to do my normal work. I didn't want to commit it because I didn't want it to apply to servers or my coworkers, but I did want to continue testing it while I made other commits rather than change to a particular branch each time."

So in that case one can run `Git update-index --assume-unchanged <path>`. Then you undo this by running `Git update-index --no-assume-unchanged <path>`.

