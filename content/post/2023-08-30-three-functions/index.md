---
title: "Three (four?) R functions I enjoyed this week"
date: '2023-08-30'
slug: three-R-functions
output: hugodown::hugo_document
rmd_hash: 5ad886b3c83e0885
tags:
  - good practice
  - code style
  - useful functions

---

There are already three functions of note on a piece of paper on my desk, so it's time to blog about them!

*This post was featured on the [R Weekly podcast](https://podverse.fm/episode/tUcOmY5AN) by Eric Nantz and Mike Thomas.*

## How does this package depend on this other package? [`pak::pkg_deps_explain()`](https://pak.r-lib.org/reference/pkg_deps_explain.html)

The [pak package by Gábor Csárdi](https://pak.r-lib.org) makes installing packages easier. If I need to start working on a package, I clone it, then run [`pak::pak()`](https://pak.r-lib.org/reference/pak.html#details) to install and update its dependencies. It's a "convenience function" that is convenient for sure! Bye bye [`remotes::install_deps()`](https://remotes.r-lib.org/reference/install_deps.html).

Anyway, pak is truly a treasure trove, even for challenges that happen less often. Earlier this week I was seeing an error in a GitHub Actions log due to a package that I didn't know was a dependency of the package I was working on. It was clearly not a direct dependency, it was not listed in `DESCRIPTION`. It was a dependency of a dependency and I couldn't guess which one it was... Luckily one doesn't need to guess! Although I didn't think of it right away, the [`pak::pkg_deps_explain()`](https://pak.r-lib.org/reference/pkg_deps_explain.html) is my friend in such cases[^1]!

For instance, if you are wondering why usethis depends on httr2,

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>pak</span><span class='nf'>::</span><span class='nf'><a href='https://pak.r-lib.org/reference/pkg_deps_explain.html'>pkg_deps_explain</a></span><span class='o'>(</span><span class='s'>"usethis"</span>, <span class='s'>"httr2"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; </span></span>
<span></span><span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> Updated metadata database: 3.97 MB in 4 files.</span></span>
<span></span><span><span class='c'>#&gt; </span></span>
<span></span><span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Updating metadata database</span></span>
<span></span><span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> Updating metadata database ... done</span></span>
<span></span><span><span class='c'>#&gt; </span></span>
<span></span><span><span class='c'>#&gt; usethis -&gt; gh -&gt; httr2</span></span>
<span></span></code></pre>

</div>

usethis depends on gh (client for GitHub API) that in turns depends on httr2.

Next time I encounter a similar problem I can only hope I'll remember about this function sooner!

## Where in the file are there non ASCII characters? `tools::showNonASCIIfile()`

If you get the dreaded R CMD check WARNING "Found the following file with non-ASCII characters" and can't see at once what character that is in the file, you don't need to comb through each line of code. You can simply run `tools::showNonASCIIfile(<filename>)`. After that an easy fix can be to replace then with the `\uxxxx` escape as indicated in the WARNING. I'm not saying it's always that easy but that's what happened to me this week! I found the correct escape by using a search engine.

## How do these two text files differ? `tools::Rdiff()` or `gert::git_diff_patch()`

I'm working on the [babeldown R package](https://docs.ropensci.org/babeldown/) that helps translate mostly Markdown files. Support for first-time automatic translation is there, but a next step is to add support for automatic update of a translation based on a git commit. Say I have a file in English and its translation in Spanish and a contributor edits part of the Spanish file. We want to have a function update the English file based on this diff. As part of that work I need to programmatically parse the difference between two text files.

I saw [`gert::git_diff()`](https://docs.ropensci.org/gert/reference/git_diff.html) but I didn't find the patch to be easily parsable.

I had better luck with [`tools::Rdiff()`](https://rdrr.io/r/tools/Rdiff.html) especially after a GitHub search showed me an example using it with `Log = TRUE`. Before that I was unsuccessfully trying [`capture.output()`](https://rdrr.io/r/utils/capture.output.html) to capture what was printed... I should have read the arguments list more carefully.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>original_lines</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span></span>
<span>  <span class='s'>"# Title"</span>, <span class='s'>""</span>,</span>
<span>  <span class='s'>"## Subtitle"</span>, <span class='s'>""</span>,</span>
<span>  <span class='s'>"Some info"</span>, <span class='s'>""</span>,</span>
<span>  <span class='s'>"First line of a paragraph"</span>,</span>
<span>  <span class='s'>"Second line of a paragraph"</span></span>
<span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>amended_lines</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span></span>
<span>  <span class='s'>"# BIG Title"</span>, <span class='s'>""</span>,</span>
<span>  <span class='s'>"## Subtitle"</span>, <span class='s'>""</span>,</span>
<span>  <span class='s'>"Some info"</span>, <span class='s'>""</span>,</span>
<span>  <span class='s'>"First line of a paragraph"</span>,</span>
<span>  <span class='s'>"Second line of a paragraph"</span>,</span>
<span>  <span class='s'>"More info"</span></span>
<span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>file1</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempfile</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nv'>file2</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempfile</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='nv'>original_lines</span>, <span class='nv'>file1</span><span class='o'>)</span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='nv'>amended_lines</span>, <span class='nv'>file2</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>diff</span> <span class='o'>&lt;-</span> <span class='nf'>tools</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/r/tools/Rdiff.html'>Rdiff</a></span><span class='o'>(</span><span class='nv'>file1</span>, <span class='nv'>file2</span>, Log <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; files differ in number of lines:</span></span>
<span></span><span><span class='nv'>diff</span></span>
<span><span class='c'>#&gt; $status</span></span>
<span><span class='c'>#&gt; [1] 1</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $out</span></span>
<span><span class='c'>#&gt; [1] "files differ in number of lines" "1c1"                            </span></span>
<span><span class='c'>#&gt; [3] "&lt; # Title"                       "---"                            </span></span>
<span><span class='c'>#&gt; [5] "&gt; # BIG Title"                   "8a9"                            </span></span>
<span><span class='c'>#&gt; [7] "&gt; More info"</span></span>
<span></span></code></pre>

</div>

What I especially liked in this output, is the lines `<line-numbers-in-original-file><letter-indicated-status><line-numbers-in-new-file>` where the status can be a (added), d (deleted) or c (changed).

In comparison here's what gert gives me.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>dir</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempdir</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_repo.html'>git_init</a></span><span class='o'>(</span><span class='nv'>dir</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='nv'>original_lines</span>, <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>dir</span>, <span class='s'>"file.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"file.txt"</span>, repo <span class='o'>=</span> <span class='nv'>dir</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;       file status staged</span></span>
<span><span class='c'>#&gt; 1 file.txt    new   TRUE</span></span>
<span></span><span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"first commit"</span>, repo <span class='o'>=</span> <span class='nv'>dir</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "b56a48d10dc033e4c6a3bb81be6e18d27b34032a"</span></span>
<span></span><span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='nv'>amended_lines</span>, <span class='nf'><a href='https://rdrr.io/r/base/file.path.html'>file.path</a></span><span class='o'>(</span><span class='nv'>dir</span>, <span class='s'>"file.txt"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='s'>"file.txt"</span>, repo <span class='o'>=</span> <span class='nv'>dir</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;       file   status staged</span></span>
<span><span class='c'>#&gt; 1 file.txt modified   TRUE</span></span>
<span></span><span><span class='nv'>commit_of_interest</span> <span class='o'>&lt;-</span> <span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span><span class='s'>"second commit"</span>, repo <span class='o'>=</span> <span class='nv'>dir</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/cat.html'>cat</a></span><span class='o'>(</span><span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_diff.html'>git_diff_patch</a></span><span class='o'>(</span><span class='nv'>commit_of_interest</span>, repo <span class='o'>=</span> <span class='nv'>dir</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; diff --git a/file.txt b/file.txt</span></span>
<span><span class='c'>#&gt; index 6d79a01..44a4269 100644</span></span>
<span><span class='c'>#&gt; --- a/file.txt</span></span>
<span><span class='c'>#&gt; +++ b/file.txt</span></span>
<span><span class='c'>#&gt; @@ -1,4 +1,4 @@</span></span>
<span><span class='c'>#&gt; -# Title</span></span>
<span><span class='c'>#&gt; +# BIG Title</span></span>
<span><span class='c'>#&gt;  </span></span>
<span><span class='c'>#&gt;  ## Subtitle</span></span>
<span><span class='c'>#&gt;  </span></span>
<span><span class='c'>#&gt; @@ -6,3 +6,4 @@ Some info</span></span>
<span><span class='c'>#&gt;  </span></span>
<span><span class='c'>#&gt;  First line of a paragraph</span></span>
<span><span class='c'>#&gt;  Second line of a paragraph</span></span>
<span><span class='c'>#&gt; +More info</span></span>
<span></span></code></pre>

</div>

I'm not exactly sure yet whether [`tools::Rdiff()`](https://rdrr.io/r/tools/Rdiff.html) will be enough for my [use case](https://mastodon.social/deck/@maelle/110972627375018302) but it might!

## Conclusion

My sticky note mentioned [`pak::pkg_deps_explain()`](https://pak.r-lib.org/reference/pkg_deps_explain.html), [`tools::showNonASCIIfile()`](https://rdrr.io/r/tools/showNonASCII.html) (what case is this :sweat_smile:) and [`tools::Rdiff()`](https://rdrr.io/r/tools/Rdiff.html), that helped me make progress on R code earlier this week. Time to break out a fresh sticky note and see what ends up on it!

[^1]: I first wrote it "pak_deps_explain" instead of "pkg_deps_explain". :grimacing:

