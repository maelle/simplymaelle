---
title: "R functions that shorten/filter stuff: less is more"
date: '2023-08-31'
slug: three-shorten
tags:
  - good practice
  - code style
  - useful functions
output: hugodown::hugo_document
rmd_hash: 9ffc89a57eceffbc

---

My sticky note is full! And luckily all functions on it can be squeezed into a similar topic: making things smaller!

## Make lists smaller with `purrr::compact()`, `purrr::keep()`, `purrr::discard()`

Once upon a time there was a list (isn't this the beginning of all R scripts?!)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>my_list</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span></span>
<span>  name <span class='o'>=</span> <span class='s'>"Maëlle"</span>,</span>
<span>  city <span class='o'>=</span> <span class='s'>"Nancy"</span>,</span>
<span>  r_problems_encountered <span class='o'>=</span> <span class='kc'>Inf</span>,</span>
<span>  python_skills <span class='o'>=</span> <span class='kc'>NULL</span></span>
<span><span class='o'>)</span></span></code></pre>

</div>

Imagine you want to get rid of `NULL` elements. That's what [`purrr::compact()`](https://purrr.tidyverse.org/reference/keep.html) does!

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/keep.html'>compact</a></span><span class='o'>(</span><span class='nv'>my_list</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; $name</span></span>
<span><span class='c'>#&gt; [1] "Maëlle"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $city</span></span>
<span><span class='c'>#&gt; [1] "Nancy"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $r_problems_encountered</span></span>
<span><span class='c'>#&gt; [1] Inf</span></span>
<span></span></code></pre>

</div>

Imagine you want to only keep elements that are infinite numbers. For some unknown reason [`is.infinite()`](https://rdrr.io/r/base/is.finite.html) works on strings (but not on `NULL`) so the following works.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>my_list</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/keep.html'>compact</a></span><span class='o'>(</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/keep.html'>keep</a></span><span class='o'>(</span><span class='nv'>is.infinite</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; $r_problems_encountered</span></span>
<span><span class='c'>#&gt; [1] Inf</span></span>
<span></span></code></pre>

</div>

Similarly, imagine you want to discard all elements that are character.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/keep.html'>discard</a></span><span class='o'>(</span><span class='nv'>my_list</span>, <span class='nv'>is.character</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; $r_problems_encountered</span></span>
<span><span class='c'>#&gt; [1] Inf</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $python_skills</span></span>
<span><span class='c'>#&gt; NULL</span></span>
<span></span></code></pre>

</div>

We've only used built-in functions as predicate functions (the second argument of all these calls). You can really unleash the power of these three functions when pairing them with your own predicate functions!

Related to these functions, don't miss Jonathan Carroll's great post ["Four Filters for Functional (Programming) Friends"](https://jcarroll.com.au/2023/08/30/four-filters-for-functional-programming-friends/).

## Make string (vectors) smaller with `stringr::str_squish()`, `stringr::str_subset()`

This week when removing stringr from the dependencies of the [glitter](https://github.com/lvaudor/glitter) package I discovered [`stringr::str_subset()`](https://stringr.tidyverse.org/reference/str_subset.html). It will help you keep, in purrr's parlance, only the elements of a string vector that have a match with a pattern.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>string</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"R"</span>, <span class='s'>"Python"</span>, <span class='s'>"Julia"</span>, <span class='s'>"Ruby"</span><span class='o'>)</span></span>
<span><span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_subset.html'>str_subset</a></span><span class='o'>(</span><span class='nv'>string</span>, <span class='s'>"R"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "R"    "Ruby"</span></span>
<span></span></code></pre>

</div>

It will also help you discard them if you use the `negate` arguments.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_subset.html'>str_subset</a></span><span class='o'>(</span><span class='nv'>string</span>, <span class='s'>"R"</span>, negate <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "Python" "Julia"</span></span>
<span></span></code></pre>

</div>

In glitter, where I chose to write similar-looking functions for the stringr functions that we used, I replaced [`stringr::str_subset()`](https://stringr.tidyverse.org/reference/str_subset.html) with

``` r
str_subset <- function(x, pattern, negate = FALSE) {
  grep(pattern, x, value = TRUE, invert = negate)
}
```

since its docs mentioned this base R's equivalence.

Another stringr function that makes things smaller is [`stringr::str_squish()`](https://stringr.tidyverse.org/reference/str_trim.html) that removes useless whitespace from a string vector: whitespace at the start and end, and repeated whitespace in the middle.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>text</span> <span class='o'>&lt;-</span> <span class='s'>" So much\n\n useless    whitespace    "</span></span>
<span><span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_trim.html'>str_squish</a></span><span class='o'>(</span><span class='nv'>text</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "So much useless whitespace"</span></span>
<span></span></code></pre>

</div>

It's documented on the same page as [`stringr::str_trim()`](https://stringr.tidyverse.org/reference/str_trim.html) which is stringr's own [`trimws()`](https://rdrr.io/r/base/trimws.html) (a base R function) that only removes whitespace from the start and end. 💇

## Make your code shorter with `purrr::partial()`

I am working on a vignette for glitter where I write (well, copy-paste) the same function call over and over again.

``` r
blablabla %>%
  spq_perform(
    endpoint = "https://ld.admin.ch/query", 
    request_type = "body-form"
  )
```

From the developer's perspective it makes me wonder whether we should add some options as default. From an user's perspective it reminded me of [`purrr::partial()`](https://purrr.tidyverse.org/reference/partial.html)! With [`purrr::partial`](https://purrr.tidyverse.org/reference/partial.html) one easily creates a function that corresponds to a function with some arguments pre-filled.

So, as a glitter user, I could use

``` r
my_spq_perform <- purrr::partial(
    spq_perform,
    endpoint = "https://ld.admin.ch/query", 
    request_type = "body-form"
)

blablabla %>%
  my_spq_perform
```

Quite neat!

## Conclusion

In this short (of course) post I presented functions for making lists, string vectors and code shorter by filtering or squishing them. 🔨

