---
title: "Introducing saperlipopette, a package to practice Git!"
date: '2024-01-18'
slug: saperlipopette-package-practice-git
output: hugodown::hugo_document
tags:
  - git
rmd_hash: b7d91f3b85a284f3

---

I got more confident with Git since reading [Git in practice](/2023/11/01/reading-notes-git-in-practice/). This has resulted in a more enjoyable Git practice! I'm also more keen to sharing Git "tips" with others, but felt it was challenging to quickly come up with examples to demo some Git workflows. This is what motivated my creating saperlipopette, an R package containing small Git playgrounds to practice various Git commands and strategies!

## What is saperlipopette?

The [saperlipopette](https://maelle.github.io/saperlipopette/) package creates Git messes, or playgrounds, that users need to solve. Part of the [exercises](https://maelle.github.io/saperlipopette/reference/index.html) are inspired by the wonderful website [Oh Shit, Git!?!](https://ohshitgit.com/) by Katie Sylor-Miller; others reflect commands that I've enjoyed using... or that I'd like to use one day, like git bisect, so the list is partly aspirational!

While saperlipopette itself makes good use of the [gert](https://docs.ropensci.org/gert/) package under the hood, the users can solve the Git messes any way they like, be it with some sort of Git interface, the command line, etc. The package provides no checking of solutions. You get a new folder with a Git mess inside, some tips, and you are the one defining success at the end of one try, by looking at your Git history. Because re-creating an exercise folder only demands your running a function, you can re-create the exercise as needed.

## Why this name?

This package is intended to be a companion to <https://ohshitgit.com/>, so its name had to honour the exclamation. "saperlipopette" is an [old-fashioned French exclamation](https://en.wiktionary.org/wiki/saperlipopette). You can say "Saperlipopette, Git!".

## How to use saperlipopette?

### Setup

You can install the development version of saperlipopette like so:

``` r
pak::pak("maelle/saperlipopette")
```

You'll also need

-   a [Git installation](https://happygitwithr.com/install-git), but if you made it here you probably already use Git at least a bit.
-   basic Git knowledge, in particular being able to examine the Git history, be it with [git log](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History) or a tool in your IDE.
-   a directory where to store the exercises folder. In all examples we use a temporary directory but if you prefer, you could use a dedicated "scratch directory".

### Basic example

Let's try the [`saperlipopette::exo_one_small_change()`](https://maelle.github.io/saperlipopette/reference/exo_one_small_change.html) that goes with ["Oh shit, I committed and immediately realized I need to make one small change!"](https://ohshitgit.com/#change-last-commit).

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='s'><a href='https://maelle.github.io/saperlipopette/'>"saperlipopette"</a></span><span class='o'>)</span></span>
<span><span class='nv'>parent_path</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempdir</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nv'>path</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://maelle.github.io/saperlipopette/reference/exo_one_small_change.html'>exo_one_small_change</a></span><span class='o'>(</span><span class='nv'>parent_path</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Follow along in /tmp/RtmpaRVGjp/file14ce858effbee/one-small-change!</span></span>
<span></span><span><span class='c'># what's in path</span></span>
<span><span class='nf'>fs</span><span class='nf'>::</span><span class='nf'><a href='https://fs.r-lib.org/reference/dir_tree.html'>dir_tree</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #0000BB; font-weight: bold;'>/tmp/RtmpaRVGjp/file14ce858effbee/one-small-change</span></span></span>
<span><span class='c'>#&gt; ├── <span style='color: #0000BB; font-weight: bold;'>R</span></span></span>
<span><span class='c'>#&gt; └── bla</span></span>
<span></span><span><span class='c'># with Git in a command line: git log</span></span>
<span><span class='c'># or the gert R package</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_log</a></span><span class='o'>(</span>repo <span class='o'>=</span> <span class='nv'>path</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 2 × 6</span></span></span>
<span><span class='c'>#&gt;   commit                          author time                files merge message</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>*</span> <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>                           <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>  <span style='color: #555555; font-style: italic;'>&lt;dttm&gt;</span>              <span style='color: #555555; font-style: italic;'>&lt;int&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;lgl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>  </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span> 2ff0d31f566e68ae0ee94b6028a3fa… Jane … 2023-12-15 <span style='color: #555555;'>16:25:00</span>     1 FALSE <span style='color: #555555;'>"</span>feat:…</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span> e227ecc55e421f70b6e30602e6a2ee… Jane … 2023-12-15 <span style='color: #555555;'>16:25:00</span>     2 FALSE <span style='color: #555555;'>"</span>First…</span></span>
<span></span></code></pre>

</div>

At this stage, the user would open the newly created R project and launch an R session, where messages would indicate them what to do, and which URL to follow, to find the corresponding ohshitgit entry if relevant. In practice here the user would change a file, then Git add it, then run `git commit --amend --no-edit`. The user would examine the [Git history](https://git-scm.com/book/en/v2/Git-Basics-Viewing-the-Commit-History) before and after this.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#&gt; <span style='color: #BB0000;'>✖</span> "Oh shit, I committed and immediately realized I need to make one small change!"</span></span>
<span></span><span><span class='c'>#&gt; <span style='color: #BB0000;'>✖</span> I wanted to list 3 things in my bla file, not only two!</span></span>
<span></span><span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> See <span style='color: #0000BB; font-style: italic;'>&lt;https://ohshitgit.com/#change-last-commit&gt;</span></span></span>
<span></span><span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> For more help use `tip()`</span></span>
<span></span></code></pre>

</div>

If they need more instructions than what is initially provided, the user can run:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>tip</span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; • Add 'thing 3' to the <span style='color: #0000BB;'>bla</span> file and save it.</span></span>
<span></span><span><span class='c'>#&gt; • Add 'bla' file to Git.</span></span>
<span></span><span><span class='c'>#&gt; • `git commit --amend --no-edit`</span></span>
<span></span><span><span class='c'>#&gt; • Examine Git history.</span></span>
<span></span></code></pre>

</div>

That interface relies on adding an (.gitignored!) `.Rprofile` to the newly created project, with instructions formatted with the [cli package](https://blog.r-hub.io/2023/11/30/cliff-notes-about-cli/).

We've set the Git author, committer and date so that the automatic commits get the same hashes, which could be useful when teaching a group: everyone should be looking at the same hashes on their machine, except for those commits they create themselves.

## Feedback welcome!

In this post I introduced the saperlipopette package whose aim is to help users practice their Git skills in a safe (because throw-away) environments! I am very grateful to Jim Gardner for [useful feedback](https://github.com/maelle/saperlipopette/issues/9) and would love to [hear](https://github.com/maelle/saperlipopette/issues) from more users, if saperlipopette is of any interest to you.

