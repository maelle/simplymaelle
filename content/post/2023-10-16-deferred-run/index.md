---
title: "The real reset button for local mess fom tests: withr::deferred_run()"
date: '2023-10-16'
slug: test-local-mess-reset
output: hugodown::hugo_document
rmd_hash: d6012d7187796c44

---

Following [last week's post on my testing workflow enhancements](/2023/10/09/test-workflow-enhancement/), Jenny Bryan kindly reminded me of the existence of an actual reset button when you've been interactively running tests that include some "local mess": [`withr::local_envvar()`](https://withr.r-lib.org/reference/with_envvar.html), [`withr::local_dir()`](https://withr.r-lib.org/reference/with_dir.html), [`usethis::local_project()`](https://usethis.r-lib.org/reference/proj_utils.html)... The reset button is [`withr::deferred_run()`](https://withr.r-lib.org/reference/defer.html).

It is documented in Jenny's article about [test fixtures](https://testthat.r-lib.org/articles/test-fixtures.html):

> Since the global environment isn't perishable, like a test environment is, you have to call deferred_run() explicitly to execute the deferred events. You can also clear them, without running, with deferred_clear().

It is also mentioned in [messages from withr](https://github.com/r-lib/withr/blob/0c5254ebc74590e80cc0056303d74b049b943920/R/defer.R#L152).

For some reason it hadn't clicked for me until now?! But now I know better!

## Example: making a `withr::local_` mess and wiping it with `withr::deferred_run()`

Imagine a test file[^1]

``` r
test_that("My function works", {
  temp_dir <- withr::local_tempdir()
  withr::local_options(blop = TRUE)
  usethis::local_project(temp_dir, force = TRUE)
  withr::local_envvar("TEST_SWITCH" = "something")
  expect_something(my_function()) # not actual test code, you get the idea
})
```

Imagine the test fails and I run this in my R session probably after throwing a [`browser()`](https://rdrr.io/r/base/browser.html)[^2] somewhere and running [`devtools::load_all()`](https://devtools.r-lib.org/reference/load_all.html):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>temp_dir</span> <span class='o'>&lt;-</span> <span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_tempfile.html'>local_tempdir</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_options.html'>local_options</a></span><span class='o'>(</span>blop <span class='o'>=</span> <span class='kc'>FALSE</span><span class='o'>)</span></span>
<span><span class='nf'>usethis</span><span class='nf'>::</span><span class='nf'><a href='https://usethis.r-lib.org/reference/proj_utils.html'>local_project</a></span><span class='o'>(</span><span class='nv'>temp_dir</span>, force <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BB00;'>✔</span> Setting active project to <span style='color: #0000BB;'>'/tmp/RtmplMLAWb/file1f4143b2c0a1'</span></span></span>
<span></span><span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_envvar.html'>local_envvar</a></span><span class='o'>(</span><span class='s'>"TEST_SWITCH"</span> <span class='o'>=</span> <span class='s'>"something"</span><span class='o'>)</span></span></code></pre>

</div>

Then I do the actual debugging. At the end my session is all messy!

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/Sys.getenv.html'>Sys.getenv</a></span><span class='o'>(</span><span class='s'>"TEST_SWITCH"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "something"</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/options.html'>getOption</a></span><span class='o'>(</span><span class='s'>"blop"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] FALSE</span></span>
<span></span><span><span class='nf'>usethis</span><span class='nf'>::</span><span class='nf'><a href='https://usethis.r-lib.org/reference/proj_sitrep.html'>proj_sitrep</a></span><span class='o'>(</span><span class='o'>)</span> <span class='c'># cool function!</span></span>
<span><span class='c'>#&gt; •   working_directory: <span style='color: #0000BB;'>'/home/maelle/Documents/blog/simplymaelle/content/post/2023-10-16-deferred-run'</span></span></span>
<span><span class='c'>#&gt; • active_usethis_proj: <span style='color: #0000BB;'>'/tmp/RtmplMLAWb/file1f4143b2c0a1'</span></span></span>
<span><span class='c'>#&gt; • active_rstudio_proj: <span style='color: #555555;'>&lt;unset&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #BB0000;'>•</span> Your working directory is not the same as the active usethis project.</span></span>
<span><span class='c'>#&gt;   Set working directory to the project: <span style='color: #555555;'>`setwd(proj_get())`</span></span></span>
<span><span class='c'>#&gt;   Set project to working directory:     <span style='color: #555555;'>`proj_set(getwd())`</span></span></span>
<span></span></code></pre>

</div>

Instead of fixing each thing by hand I can run

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/defer.html'>deferred_run</a></span><span class='o'>(</span><span class='o'>)</span></span></code></pre>

</div>

And now all is right in my session again, you'll have to believe me or run the example yourself: indeed, demonstrating all of this in a knitr document makes it a bit more challenging. I swear that outside of a knitr document, this all works even for code that changes the current directory!

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/Sys.getenv.html'>Sys.getenv</a></span><span class='o'>(</span><span class='s'>"TEST_SWITCH"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/options.html'>getOption</a></span><span class='o'>(</span><span class='s'>"blop"</span><span class='o'>)</span></span>
<span><span class='nf'>usethis</span><span class='nf'>::</span><span class='nf'><a href='https://usethis.r-lib.org/reference/proj_utils.html'>proj_get</a></span><span class='o'>(</span><span class='o'>)</span></span></code></pre>

</div>

So I can go and debug the next failing test. :smile_cat:

## What about rlang's local mess?

rlang itself has [`rlang::local_interactive()`](https://rlang.r-lib.org/reference/is_interactive.html) and [`rlang::local_options()`](https://rlang.r-lib.org/reference/local_options.html). Luckily they are reset by [`withr::deferred_run()`](https://withr.r-lib.org/reference/defer.html).

I'm not sure exactly where the compatibility comes from, maybe from [the standalone defer script](https://github.com/r-lib/rlang/blob/7a78dc3f0d5b2fb73289f820e39afb5c4d665802/R/import-standalone-defer.R). In any case, that's good news!

## Conclusion

So, in summary, if you're running test code interactively to debug them, and this test code changed some things using [`withr::local_`](https://withr.r-lib.org/reference/with_.html) or other compatible `local_` functions, to get your session back to its initial state without restarting R, run [`withr::deferred_run()`](https://withr.r-lib.org/reference/defer.html)! Thanks Jenny for the reminder!

[^1]: [Test switches](https://blog.r-hub.io/2023/01/23/code-switch-escape-hatch-test/) are sooo handy.

[^2]: For better debugging advice, refer to [Shannon Pileggi's materials](https://www.pipinghotdata.com/talks/2022-11-11-debugging/)!

