---
title: "Three useful (to me) R notions"
date: '2023-07-24'
tags:
  - good practice
  - code style
  - useful functions
slug: basic-notions
output: hugodown::hugo_document
rmd_hash: 00b1013fa5b235bd

---

Following my recent post on [three useful (to me) R patterns](/2023/06/06/basic-patterns/), I've written down three other things on a tiny sticky note. This post will allow me to throw away this beaten down sticky note, and maybe to show you one element you didn't know?

## `nzchar()`: "a fast way to find out if elements of a character vector are non-empty strings"

One of my favorite testing technique is the escape hatch strategy, about which I wrote a [post on the R-hub blog](https://blog.r-hub.io/2023/01/23/code-switch-escape-hatch-test/): you make part of your code responsive to an environment variable, and you locally set that environment variable in your tests. In your code, to determine whether the environment variable has been set to any non-empty string, you can use the [`nzchar()`](https://rdrr.io/r/base/nchar.html) function.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='s'>"Hello World"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='s'>""</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] FALSE</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='kc'>NA</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='kc'>NA</span>, keepNA <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] NA</span></span>
<span></span><span></span>
<span><span class='c'># Now with an environment variable</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/Sys.getenv.html'>Sys.getenv</a></span><span class='o'>(</span><span class='s'>"blopblopblop"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] FALSE</span></span>
<span></span><span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_envvar.html'>with_envvar</a></span><span class='o'>(</span></span>
<span>  new <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"blopblopblop"</span> <span class='o'>=</span> <span class='s'>"bla"</span><span class='o'>)</span>,</span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/Sys.getenv.html'>Sys.getenv</a></span><span class='o'>(</span><span class='s'>"blopblopblop"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span></code></pre>

</div>

Allow me to mention the obvious: if you're using [`nzchar()`](https://rdrr.io/r/base/nchar.html) to examine an environment variable, do not forget [`Sys.getenv()`](https://rdrr.io/r/base/Sys.getenv.html). I spent too much time looking at the following piece of code last week: `nzchar("BABELQUARTO_TESTS_URL")` wondering why it didn't do what I expected it to do... I had to replace it with `nzchar(Sys.getenv("BABELQUARTO_TESTS_URL"))`.

## The backports R package, and its README

R is quite stable to say the least, but useful new functions have been added to it over time. For instance, R 3.3.0 saw the birth of [`trimws()`](https://rdrr.io/r/base/trimws.html), that trims white space.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/trimws.html'>trimws</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"useless whitespace "</span>, <span class='s'>" all around  "</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "useless whitespace" "all around"</span></span>
<span></span></code></pre>

</div>

To use such functions in your package, you might need to set a dependency on a recent version of R, making it harder for users with restrictive rights on their R install, to use your package. The [backports R package](https://github.com/r-lib/backports) can help you [avoid to set that dependency](https://blog.r-hub.io/2022/09/12/r-dependency/): it provides backports for these useful new functions. The README explains how to set up its usage.

I like both the existence of backports and its README because it is a list of these newer functions! I can't pretend I'm a hipsteR but there are gems I forget or never heard about. Reading the list helps me see what's there! For similar reasons I find the [reference index of the lintr package](https://lintr.r-lib.org/reference/index.html) to be a treasure trove.

## Using the first non-missing thing: coalescing

Sometimes you might have an ordered "wish list" in your code: use A, then if A is missing, use B, then if B is missing, use C, and so on and so forth until a default value or error. The pkgdown package for instance has a function called [`path_first_existing()`](https://github.com/r-lib/pkgdown/blob/c9206802f2888992de92aa41f517ba7812f05331/R/utils-fs.R#L75).

I only recently realized this idea is usually called *coalescing*. dplyr has a [`coalesce()`](https://dplyr.tidyverse.org/reference/coalesce.html) function.

> "Given a set of vectors, coalesce() finds the first non-missing value at each position. It's inspired by the SQL COALESCE function which does the same thing for SQL NULLs."

I know close to NULL about SQL, but I did notice the `COALESCE` function in another query language, SPARQL, for which Lise Vaudor and I are working on a DSL, meaning a helper package, called [glitter](https://lvaudor.github.io/glitter/).

It makes me very happy to learn such an useful new word, or at least it usage in the programming realm.

## Conclusion

After writing a bit about [`nzchar()`](https://rdrr.io/r/base/nchar.html), the backports package and the idea of coalescence, I can now throw away my sticky note! Let's see what makes its way onto a new sticky note...

