---
title: "3 (actually 4) neat R functions"
date: '2023-10-20'
slug: three-neat-functions
output: hugodown::hugo_document
rmd_hash: 2cfe0c5f95f4352c
tags:
  - good practice
  - code style
  - useful functions
---

Time for me to throw away my sticky note after sharing what I wrote on it!

## `grep(...)` not `which(grepl(...))`

Recently I caught myself using `which(grepl(...))`,

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"bird"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/which.html'>which</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grepl</a></span><span class='o'>(</span><span class='s'>"i"</span>, <span class='nv'>animals</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] 2 4</span></span>
<span></span></code></pre>

</div>

when the simpler alternative is

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"bird"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grep</a></span><span class='o'>(</span><span class='s'>"i"</span>, <span class='nv'>animals</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] 2 4</span></span>
<span></span></code></pre>

</div>

And should I need the values instead of the indices, I know I shouldn't write

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"bird"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nv'>animals</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grepl</a></span><span class='o'>(</span><span class='s'>"i"</span>, <span class='nv'>animals</span><span class='o'>)</span><span class='o'>]</span></span>
<span><span class='c'>#&gt; [1] "bird" "fish"</span></span>
<span></span></code></pre>

</div>

but

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"bird"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grep</a></span><span class='o'>(</span><span class='s'>"i"</span>, <span class='nv'>animals</span>, value <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "bird" "fish"</span></span>
<span></span></code></pre>

</div>

How to remember to use [`grep()`](https://rdrr.io/r/base/grep.html)? Re-reading oneself, or having code reviewed, probably helps, but why not automate this? When I shared my note to self on Mastodon, Hugo Gruson [explained](https://mastodon.social/deck/@grusonh/111181373365621067) that detecting usage of `which(grepl(` was part of planned linters to be added to lintr from [Google linting suite](https://github.com/r-lib/lintr/issues/884). This is excellent news!

## `strrep()` and other defence tools against poor usages of `paste()`

Yihui Xie wrote a [blog post inspired by my own series](https://yihui.org/en/2023/10/three-functions/), where one of the three presented functions was one that was on my sticky note! I'll still present it: [`strrep()`](https://rdrr.io/r/base/strrep.html).

[`strrep()`](https://rdrr.io/r/base/strrep.html) means "string repeat". Instead of writing

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/rep.html'>rep</a></span><span class='o'>(</span><span class='s'>"bla"</span>, <span class='m'>3</span><span class='o'>)</span>, collapse <span class='o'>=</span> <span class='s'>""</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "blablabla"</span></span>
<span></span></code></pre>

</div>

you can, and should, write

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/strrep.html'>strrep</a></span><span class='o'>(</span><span class='s'>"bla"</span>, <span class='m'>3</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "blablabla"</span></span>
<span></span></code></pre>

</div>

I discovered this function because Hugo Gruson telling me about lintr inspired me to skim through lintr reference, where I saw ["Raise lints for several common poor usages of `paste()`"](https://lintr.r-lib.org/reference/paste_linter.html). That linter would also tell you when you use `paste(, sep = "")` instead of [`paste0()`](https://rdrr.io/r/base/paste.html).

## `startsWith()` and `endsWith()`

I learned about [`startsWith()`](https://rdrr.io/r/base/startsWith.html) and [`endsWith()`](https://rdrr.io/r/base/startsWith.html) by reading lintr reference but I also got notified about it when running lintr on a package I was working on. Have you ever tried [running all linters on your code](https://github.com/r-lib/lintr/issues/1482#issuecomment-1198590483)? Fun experience. Anyhow, one linter is [Require usage of `startsWith()` and `endsWith()` over `grepl()`/`substr()` versions](https://lintr.r-lib.org/reference/string_boundary_linter.html), with an interesting Details section on missing values.

Instead of writing

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"cow"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grepl</a></span><span class='o'>(</span><span class='s'>"^c"</span>, <span class='nv'>animals</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1]  TRUE  TRUE FALSE FALSE</span></span>
<span></span></code></pre>

</div>

I should write

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"cow"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/startsWith.html'>startsWith</a></span><span class='o'>(</span><span class='nv'>animals</span>, <span class='s'>"c"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1]  TRUE  TRUE FALSE FALSE</span></span>
<span></span></code></pre>

</div>

A nice side-effect of the switch, beyond good practice for its own sake and more readability, is that the argument order is more logical in [`startsWith()`](https://rdrr.io/r/base/startsWith.html).

Similarly, instead of writing

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"cow"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grepl</a></span><span class='o'>(</span><span class='s'>"t$"</span>, <span class='nv'>animals</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1]  TRUE FALSE FALSE FALSE</span></span>
<span></span></code></pre>

</div>

I should write

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"cat"</span>, <span class='s'>"cow"</span>, <span class='s'>"dog"</span>, <span class='s'>"fish"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/startsWith.html'>endsWith</a></span><span class='o'>(</span><span class='nv'>animals</span>, <span class='s'>"t"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1]  TRUE FALSE FALSE FALSE</span></span>
<span></span></code></pre>

</div>

## Conclusion

In this post I shared about [`grep()`](https://rdrr.io/r/base/grep.html) to be used in lieu of `which(grepl())`, about [`strrep()`](https://rdrr.io/r/base/strrep.html) (string repetition) to be used in lieu of `paste(rep(), collapse ="")` and about [`startsWith()`](https://rdrr.io/r/base/startsWith.html) and [`endsWith()`](https://rdrr.io/r/base/startsWith.html) to be used in lieu of some regular expressions with respectively `^` and `$`.

