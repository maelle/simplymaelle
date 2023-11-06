---
title: "Useful functions for dealing with object names"
date: '2023-11-06'
slug: functions-dealing-with-names
output: hugodown::hugo_document
rmd_hash: 1c7b3de33ea27a85

---

My sticky note filled up quickly after I only added [`setNames()`](https://rdrr.io/r/stats/setNames.html) on it, with related functions for dealing with object names, in base R and beyond!

## (Un)Setting object names: `stats::setNames()`, `unname()` and `rlang::set_names()`

I noticed a function ending with something like this:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>blop</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  </span>
<span>  <span class='c'># code creating the df data.frame</span></span>
<span>  <span class='c'># ...</span></span>
<span>  </span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/names.html'>names</a></span><span class='o'>(</span><span class='nv'>df</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"col1"</span>, <span class='s'>"col2"</span><span class='o'>)</span></span>
<span>  <span class='nv'>df</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

It struck me as simplifiable by:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>blop</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  </span>
<span>  <span class='c'># code creating the df data.frame</span></span>
<span>  <span class='c'># ...</span></span>
<span>  </span>
<span>  <span class='nf'>stats</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/r/stats/setNames.html'>setNames</a></span><span class='o'>(</span><span class='nv'>df</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"col1"</span>, <span class='s'>"col2"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

Interestingly the docs for [`setNames()`](https://rdrr.io/r/stats/setNames.html) sound as if it were created just for this use case!

> "This is a convenience function that sets the names on an object and returns the object. It is most useful at the end of a function definition where one is creating the object to be returned and would prefer not to store it under a name just so the names can be assigned."

For the opposite operation, removing the names of an object, we can use [`unname()`](https://rdrr.io/r/base/unname.html).

Unsurprisingly, the rlang package has its own special [`rlang::set_names()`](https://rlang.r-lib.org/reference/set_names.html), that is more or less a [`stats::setNames()`](https://rdrr.io/r/stats/setNames.html) + [`unname()`](https://rdrr.io/r/base/unname.html) + generic [`janitor::clean_names()`](https://sfirke.github.io/janitor/reference/clean_names.html) combo!

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='o'>(</span><span class='nv'>df</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/data.frame.html'>data.frame</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>2</span><span class='o'>)</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>5</span>, <span class='m'>7</span><span class='o'>)</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt;   c.1..2. c.5..7.</span></span>
<span><span class='c'>#&gt; 1       1       5</span></span>
<span><span class='c'>#&gt; 2       2       7</span></span>
<span></span><span></span>
<span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/set_names.html'>set_names</a></span><span class='o'>(</span><span class='nv'>df</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"a"</span>, <span class='s'>"b"</span><span class='o'>)</span><span class='o'>)</span> <span class='c'># sets names!</span></span>
<span><span class='c'>#&gt;   a b</span></span>
<span><span class='c'>#&gt; 1 1 5</span></span>
<span><span class='c'>#&gt; 2 2 7</span></span>
<span></span><span></span>
<span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/set_names.html'>set_names</a></span><span class='o'>(</span><span class='nv'>df</span>, <span class='kc'>NULL</span><span class='o'>)</span> <span class='c'># removes names</span></span>
<span><span class='c'>#&gt;      </span></span>
<span><span class='c'>#&gt; 1 1 5</span></span>
<span><span class='c'>#&gt; 2 2 7</span></span>
<span></span><span></span>
<span><span class='nv'>df2</span> <span class='o'>&lt;-</span> <span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/set_names.html'>set_names</a></span><span class='o'>(</span><span class='nv'>df</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"SHOUTING"</span>, <span class='s'>"LOUDLY"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='nv'>df2</span></span>
<span><span class='c'>#&gt;   SHOUTING LOUDLY</span></span>
<span><span class='c'>#&gt; 1        1      5</span></span>
<span><span class='c'>#&gt; 2        2      7</span></span>
<span></span><span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/set_names.html'>set_names</a></span><span class='o'>(</span><span class='nv'>df2</span>, <span class='nv'>tolower</span><span class='o'>)</span> <span class='c'># using a function to transform names</span></span>
<span><span class='c'>#&gt;   shouting loudly</span></span>
<span><span class='c'>#&gt; 1        1      5</span></span>
<span><span class='c'>#&gt; 2        2      7</span></span>
<span></span></code></pre>

</div>

Of note,

> "`set_names()` is stable and exported in purrr".

So if you prefer, you can use [`purrr::set_names()`](https://rlang.r-lib.org/reference/set_names.html) instead.

Last but not least, despite the examples above being on a data.frame, these functions can be used on lists, vectors...

## Extracting names: `names()`, `rlang::names2()`

[`names()`](https://rdrr.io/r/base/names.html) probably doesn't need any introduction, but it's interesting to mention it in contrast to [`rlang::names2()`](https://rlang.r-lib.org/reference/names2.html) that " always returns a character vector, even when an object does not have a names attribute.".

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>vector</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>2</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/names.html'>names</a></span><span class='o'>(</span><span class='nv'>vector</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; NULL</span></span>
<span></span><span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/names2.html'>names2</a></span><span class='o'>(</span><span class='nv'>vector</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "" ""</span></span>
<span></span></code></pre>

</div>

rlang also has a replacement variant, `rlang::names2<-`, that "never adds NA names and instead fills unnamed vectors with `""`."

I didn't know all this before skimming the list of rlang functions [dealing with object attributes](https://rlang.r-lib.org/reference/index.html#attributes).

## Checking names

### Is the object named: `rlang::is_named()`, `rlang::is_named2()`, `rlang::have_name()`

[`rlang::have_name()`](https://rlang.r-lib.org/reference/is_named.html) is the vectorized version of `rlang::is_name()`, not to be confused with [`rlang::has_name()`](https://rlang.r-lib.org/reference/has_name.html). :sweat_smile:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/is_named.html'>is_named</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span>a <span class='o'>=</span> <span class='m'>1</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span><span></span>
<span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/is_named.html'>is_named</a></span><span class='o'>(</span><span class='kc'>NULL</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] FALSE</span></span>
<span></span><span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/is_named.html'>is_named2</a></span><span class='o'>(</span><span class='kc'>NULL</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span><span></span>
<span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/is_named.html'>have_name</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span>a <span class='o'>=</span> <span class='m'>1</span>, <span class='m'>2</span><span class='o'>)</span><span class='o'>)</span> <span class='c'># the example from the manual page</span></span>
<span><span class='c'>#&gt; [1]  TRUE FALSE</span></span>
<span></span></code></pre>

</div>

I do not know of a base R equivalent, I mean I'd find a way to express it, but not as directly. But maybe I am missing yet another base R gem!

### Does the object have an element called X, does the object have elements called X, Y: `utils::hasName()`, `rlang::has_name()`

If you want to check that an object contains elements with certain names, you can use [`utils::hasName()`](https://rdrr.io/r/utils/hasName.html)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>expected_names</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"pof"</span>, <span class='s'>"blop"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>vector1</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span>pof <span class='o'>=</span> <span class='m'>1</span>, bla <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span><span class='nv'>vector2</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span>pof <span class='o'>=</span> <span class='m'>1</span>, bla <span class='o'>=</span> <span class='m'>2</span>, blop <span class='o'>=</span> <span class='m'>3</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>utils</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/r/utils/hasName.html'>hasName</a></span><span class='o'>(</span><span class='nv'>vector1</span>, <span class='nv'>expected_names</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1]  TRUE FALSE</span></span>
<span></span><span><span class='nf'>utils</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/r/utils/hasName.html'>hasName</a></span><span class='o'>(</span><span class='nv'>vector2</span>, <span class='nv'>expected_names</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE TRUE</span></span>
<span></span></code></pre>

</div>

The rlang package has a similar function, [`rlang::has_name()`](https://rlang.r-lib.org/reference/has_name.html).

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>expected_names</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"pof"</span>, <span class='s'>"blop"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>vector1</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span>pof <span class='o'>=</span> <span class='m'>1</span>, bla <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span><span class='nv'>vector2</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span>pof <span class='o'>=</span> <span class='m'>1</span>, bla <span class='o'>=</span> <span class='m'>2</span>, blop <span class='o'>=</span> <span class='m'>3</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/has_name.html'>has_name</a></span><span class='o'>(</span><span class='nv'>vector1</span>, <span class='nv'>expected_names</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1]  TRUE FALSE</span></span>
<span></span><span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/has_name.html'>has_name</a></span><span class='o'>(</span><span class='nv'>vector2</span>, <span class='nv'>expected_names</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE TRUE</span></span>
<span></span></code></pre>

</div>

I'm honestly not sure whether there's any difference between the utils and rlang versions, apart from the name!

### In package tests: `testthat::expect_named()`

[`testthat::expect_named()`](https://testthat.r-lib.org/reference/expect_named.html) allows you to check that an object returned by your code has the names you expect, or simply has names. You can ignore case and order based on the arguments `ignore.case` and `ignore.order`.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='o'>(</span><span class='nv'>object</span> <span class='o'>&lt;-</span> <span class='nf'>tibble</span><span class='nf'>::</span><span class='nf'><a href='https://tibble.tidyverse.org/reference/tibble.html'>tibble</a></span><span class='o'>(</span>a <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>1</span>, <span class='m'>2</span><span class='o'>)</span>, b <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='m'>3</span>, <span class='m'>4</span><span class='o'>)</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 2 × 2</span></span></span>
<span><span class='c'>#&gt;       a     b</span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;dbl&gt;</span></span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span>     1     3</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span>     2     4</span></span>
<span></span><span></span>
<span><span class='nf'>testthat</span><span class='nf'>::</span><span class='nf'><a href='https://testthat.r-lib.org/reference/expect_named.html'>expect_named</a></span><span class='o'>(</span><span class='nv'>object</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>unnamed_object</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/unname.html'>unname</a></span><span class='o'>(</span><span class='nv'>object</span><span class='o'>)</span></span>
<span><span class='nf'>testthat</span><span class='nf'>::</span><span class='nf'><a href='https://testthat.r-lib.org/reference/expect_named.html'>expect_named</a></span><span class='o'>(</span><span class='nv'>unnamed_object</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; Error: `unnamed_object` does not have names.</span></span>
<span></span><span></span>
<span><span class='nf'>testthat</span><span class='nf'>::</span><span class='nf'><a href='https://testthat.r-lib.org/reference/expect_named.html'>expect_named</a></span><span class='o'>(</span><span class='nv'>object</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"b"</span>, <span class='s'>"a"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; Error: Names of `object` ('a', 'b') don't match 'b', 'a'</span></span>
<span></span><span><span class='nf'>testthat</span><span class='nf'>::</span><span class='nf'><a href='https://testthat.r-lib.org/reference/expect_named.html'>expect_named</a></span><span class='o'>(</span><span class='nv'>object</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"b"</span>, <span class='s'>"a"</span><span class='o'>)</span>, ignore.order <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>testthat</span><span class='nf'>::</span><span class='nf'><a href='https://testthat.r-lib.org/reference/expect_named.html'>expect_named</a></span><span class='o'>(</span><span class='nv'>object</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"A"</span>, <span class='s'>"B"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; Error: Names of `object` ('a', 'b') don't match 'A', 'B'</span></span>
<span></span><span><span class='nf'>testthat</span><span class='nf'>::</span><span class='nf'><a href='https://testthat.r-lib.org/reference/expect_named.html'>expect_named</a></span><span class='o'>(</span><span class='nv'>object</span>, <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"A"</span>, <span class='s'>"B"</span><span class='o'>)</span>, ignore.case <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span></code></pre>

</div>

If you're not used to using this expectation yet, lintr can [help you a bit](https://lintr.r-lib.org/reference/expect_named_linter.html).

## Conclusion

In this post I went through functions that deals with names: [`stats::setNames()`](https://rdrr.io/r/stats/setNames.html), [`unname()`](https://rdrr.io/r/base/unname.html) and [`rlang::set_names()`](https://rlang.r-lib.org/reference/set_names.html) for (un)setting names (my initial motivation for this post!); [`names()`](https://rdrr.io/r/base/names.html), [`rlang::names2()`](https://rlang.r-lib.org/reference/names2.html) for extracting names; [`rlang::is_named()`](https://rlang.r-lib.org/reference/is_named.html), [`rlang::is_named2()`](https://rlang.r-lib.org/reference/is_named.html) and `rlang::have_names()` to find out whether an object is named; [`utils::hasName()`](https://rdrr.io/r/utils/hasName.html) and [`rlang::has_name()`](https://rlang.r-lib.org/reference/has_name.html) to find out whether an object contains elements of a given name; [`testthat::expect_named()`](https://testthat.r-lib.org/reference/expect_named.html) for testing whether an object has names, or specific names. Any other related function that comes to mind?

