---
title: "What I edit when refactoring a test file"
date: '2024-05-23'
slug: refactoring-tests
output: hugodown::hugo_document
tags:
  - package developments
  - refactoring
rmd_hash: 8fde69c43d4eddf7

---

I'm currently refactoring test files in a package. Beside some [automatic refactoring](/2024/05/15/refactoring-xml/), I am also manually updating lines of code. Here are some tips (or pet peeves, based on how I look at it / how tired I am :grin:)

## Prequel: please read the R packages book

The new edition of the R Packages book by Hadley Wickham and Jenny Bryan features three chapters on testing, all well worth a read. The ["High-level principles for testing"](https://r-pkgs.org/testing-design.html#sec-testing-design-principles) in particular are good to keep in mind.

## Use informative variable or function names

If your test is

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>test_that</span><span class='o'>(</span><span class='s'>"function_maelle_does_not_know_well() works"</span>, <span class='o'>&#123;</span></span>
<span>  <span class='nv'>q</span> <span class='o'>&lt;-</span> <span class='nf'>function_maelle_does_not_know_well</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span><span class='o'>)</span></span>
<span>  <span class='nv'>ab</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>    <span class='nv'>y</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/Log.html'>exp</a></span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='o'>+</span> <span class='m'>42</span> <span class='o'>-</span> <span class='m'>7</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/Round.html'>round</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/Log.html'>log</a></span><span class='o'>(</span><span class='nv'>y</span> <span class='o'>/</span> <span class='m'>50</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span>  <span class='nf'>expect_equal</span><span class='o'>(</span><span class='nv'>q</span>, <span class='nf'>ab</span><span class='o'>(</span><span class='m'>6</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span><span class='o'>)</span></span></code></pre>

</div>

you might want to rename `q` and `ab` to something more informative. Note that this is a fictional example, it does not make any mathematical sense.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>test_that</span><span class='o'>(</span><span class='s'>"function_maelle_does_not_know_well() works"</span>, <span class='o'>&#123;</span></span>
<span>  <span class='nv'>transformed_vector</span> <span class='o'>&lt;-</span> <span class='nf'>function_maelle_does_not_know_well</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span><span class='o'>)</span></span>
<span>  <span class='nv'>crude_transformation_approximation</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>    <span class='nv'>y</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/Log.html'>exp</a></span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='o'>+</span> <span class='m'>42</span> <span class='o'>-</span> <span class='m'>7</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/Round.html'>round</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/Log.html'>log</a></span><span class='o'>(</span><span class='nv'>y</span> <span class='o'>/</span> <span class='m'>50</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span>  <span class='nf'>expect_equal</span><span class='o'>(</span><span class='nv'>transformed_vector</span>, <span class='nf'>crude_transformation_approximation</span><span class='o'>(</span><span class='m'>6</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span><span class='o'>)</span></span></code></pre>

</div>

Imagine it will be me reading your test file, and I know *nothing* about your use case. More seriously, that's one of the numerous aspects [code review](https://code-review.tidyverse.org/) can help with.

## Vary variable names within tests

To prevent copy-pasting mistakes and reading fatigue,

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>test_that</span><span class='o'>(</span><span class='s'>"awesome_function() works"</span>, <span class='o'>&#123;</span></span>
<span>  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"easy"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>x</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>x</span>, <span class='m'>5</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"medium"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>x</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>x</span>, <span class='m'>15</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"hard"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>x</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>x</span>, <span class='m'>25</span><span class='o'>)</span></span>
<span>  </span>
<span><span class='o'>&#125;</span><span class='o'>)</span></span></code></pre>

</div>

is in my opinion better as

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>test_that</span><span class='o'>(</span><span class='s'>"awesome_function() works"</span>, <span class='o'>&#123;</span></span>
<span>  <span class='nv'>easy_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"easy"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>easy_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>easy_output</span>, <span class='m'>5</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>medium_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"medium"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>medium_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>medium_output</span>, <span class='m'>15</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>hard_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"hard"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>hard_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>hard_output</span>, <span class='m'>25</span><span class='o'>)</span></span>
<span>  </span>
<span><span class='o'>&#125;</span><span class='o'>)</span></span></code></pre>

</div>

I do not hate it when several tests use the same variable names though.

## Put the object as close as possible to the related expectation(s)

Imagine I made the code chunk from above

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>test_that</span><span class='o'>(</span><span class='s'>"awesome_function() works"</span>, <span class='o'>&#123;</span></span>
<span>  <span class='nv'>easy_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"easy"</span><span class='o'>)</span></span>
<span>  <span class='nv'>medium_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"medium"</span><span class='o'>)</span></span>
<span>  <span class='nv'>hard_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"hard"</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>easy_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>easy_output</span>, <span class='m'>5</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>medium_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>medium_output</span>, <span class='m'>15</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>hard_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>hard_output</span>, <span class='m'>25</span><span class='o'>)</span></span>
<span>  </span>
<span><span class='o'>&#125;</span><span class='o'>)</span></span></code></pre>

</div>

I much prefer the version

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>test_that</span><span class='o'>(</span><span class='s'>"awesome_function() works"</span>, <span class='o'>&#123;</span></span>
<span>  <span class='nv'>easy_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"easy"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>easy_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>easy_output</span>, <span class='m'>5</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>medium_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"medium"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>medium_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>medium_output</span>, <span class='m'>15</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>hard_output</span> <span class='o'>&lt;-</span> <span class='nf'>awesome_function</span><span class='o'>(</span><span class='m'>1</span><span class='o'>:</span><span class='m'>10</span>, method <span class='o'>=</span> <span class='s'>"hard"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_typeof</span><span class='o'>(</span><span class='nv'>hard_output</span>, <span class='s'>"double"</span><span class='o'>)</span></span>
<span>  <span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>hard_output</span>, <span class='m'>25</span><span class='o'>)</span></span>
<span>  </span>
<span><span class='o'>&#125;</span><span class='o'>)</span></span></code></pre>

</div>

because reading the expectations doesn't require me to go too far back or to remember too much.

In some cases, if the object creation is simple, and it is used in one expectation only, why not put it *inside* the expectation rather than having to assign it to an object with an informative name?

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>the_log</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/Log.html'>log</a></span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span></span>
<span><span class='nf'>expect_gt</span><span class='o'>(</span><span class='nv'>the_log</span>, <span class='m'>1</span><span class='o'>)</span></span></code></pre>

</div>

vs.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>expect_gt</span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/Log.html'>log</a></span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span>, <span class='m'>1</span><span class='o'>)</span></span></code></pre>

</div>

## Split the tests as needed, navigate them seamlessly

Splitting the tests (into several `test_that()`) means each one is more focused, easier to read, easier to investigate when failing (tests tend to fail).

In [RStudio IDE](https://github.com/rstudio/rstudio/pull/11108), each `test_that()` calls is shown in the script outline (the table of contents on the right) so you can hop from one to the other at will.

## Use a bug summary, not number, as test name

Instead of

    test_that("Bug #666 is fixed", {
      ...
    })

I prefer

    test_that("awesome_function() does not remove the ordering", {
      # <link-to-bug-report>
      ...
    })

because:

-   I can understand what the test is about without clicking the link;
-   If your repository moved for instance from a monorepo to a regular repository, the hyperlink added by RStudio IDE might no longer be valid.

## Conclusion

In this post I summarized my testing preferences *du jour*. Do you have any tips for refactoring test scripts?

