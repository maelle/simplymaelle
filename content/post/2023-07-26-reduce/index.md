---
title: "Reducing my for loop usage with purrr::reduce()"
date: '2023-07-26'
tags:
  - good practice
  - code style
slug: reduce
output: hugodown::hugo_document
rmd_hash: 8a8d754d417ed683

---

I (only! but luckily!) recently got introduced to the magic of [`purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html). *Thank you, [Tobias](https://github.com/TSchiefer)!* I was told about it right as I was unhappily using many for loops in a package[^1], for lack of a better idea. In this post I'll explain how [`purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html) helped me reduce my for loop usage. I also hope that if I'm doing something wrong, [someone will come forward and tell me](https://xkcd.com/386/)!

*This post was featured on the [R Weekly podcast](https://podverse.fm/episode/1odIoSlJl) by Eric Nantz and Mike Thomas.*

## Before: many for, much sadness

I was starting from a thing, that could be a list, or even a data.frame. Then for a bunch of variables, I tweaked the thing. My initial coding pattern was therefore:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'>for</span> <span class='o'>(</span><span class='nv'>var</span> <span class='kr'>in</span> <span class='nv'>variables_vector</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>thing</span> <span class='o'>&lt;-</span> <span class='nf'>do_something</span><span class='o'>(</span><span class='nv'>thing</span>, <span class='nv'>var</span>, other_argument <span class='o'>=</span> <span class='nv'>other_argument</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

I was iteratively changing the thing, along a `variables_vector`, or sometimes a `variables_list`.

### Silly example

Ugh, finding an example is hard, it feels very contrived but I promise my real-life adoption of `purrr::usage()` was life-changing!

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># Some basic movie information</span></span>
<span><span class='nv'>movies</span> <span class='o'>&lt;-</span> <span class='nf'>tibble</span><span class='nf'>::</span><span class='nf'><a href='https://tibble.tidyverse.org/reference/tribble.html'>tribble</a></span><span class='o'>(</span></span>
<span>  <span class='o'>~</span><span class='nv'>title</span>, <span class='o'>~</span><span class='nv'>color</span>, <span class='o'>~</span><span class='nv'>elements</span>,</span>
<span>  <span class='s'>"Barbie"</span>, <span class='s'>"pink"</span>, <span class='s'>"shoes"</span>,</span>
<span>  <span class='s'>"Oppenheimer"</span>, <span class='s'>"red"</span>, <span class='s'>"history"</span></span>
<span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># More information to add to movies</span></span>
<span><span class='nv'>info_list</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>title <span class='o'>=</span> <span class='s'>"Barbie"</span>, info <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>element <span class='o'>=</span> <span class='s'>"sparkles"</span><span class='o'>)</span><span class='o'>)</span>,</span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>title <span class='o'>=</span> <span class='s'>"Barbie"</span>, info <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>element <span class='o'>=</span> <span class='s'>"feminism"</span><span class='o'>)</span><span class='o'>)</span>,</span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>title <span class='o'>=</span> <span class='s'>"Oppenheimer"</span>, info <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>element <span class='o'>=</span> <span class='s'>"fire"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># Don't tell me this is weirdly formatted data,</span></span>
<span><span class='c'># who never obtains weirdly formatted data?!</span></span>
<span><span class='nv'>info_list</span></span>
<span><span class='c'>#&gt; [[1]]</span></span>
<span><span class='c'>#&gt; [[1]]$title</span></span>
<span><span class='c'>#&gt; [1] "Barbie"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; [[1]]$info</span></span>
<span><span class='c'>#&gt; [[1]]$info$element</span></span>
<span><span class='c'>#&gt; [1] "sparkles"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; [[2]]</span></span>
<span><span class='c'>#&gt; [[2]]$title</span></span>
<span><span class='c'>#&gt; [1] "Barbie"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; [[2]]$info</span></span>
<span><span class='c'>#&gt; [[2]]$info$element</span></span>
<span><span class='c'>#&gt; [1] "feminism"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; [[3]]</span></span>
<span><span class='c'>#&gt; [[3]]$title</span></span>
<span><span class='c'>#&gt; [1] "Oppenheimer"</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; [[3]]$info</span></span>
<span><span class='c'>#&gt; [[3]]$info$element</span></span>
<span><span class='c'>#&gt; [1] "fire"</span></span>
<span></span><span></span>
<span><span class='nv'>add_element</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>info</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>movies</span><span class='o'>[</span><span class='nv'>movies</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>==</span> <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span>,<span class='o'>]</span><span class='o'>[[</span><span class='s'>"elements"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>&lt;-</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/toString.html'>toString</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span></span>
<span>      <span class='nv'>movies</span><span class='o'>[</span><span class='nv'>movies</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>==</span> <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span>,<span class='o'>]</span><span class='o'>[[</span><span class='s'>"elements"</span><span class='o'>]</span><span class='o'>]</span>,</span>
<span>      <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"info"</span><span class='o'>]</span><span class='o'>]</span><span class='o'>[[</span><span class='m'>1</span><span class='o'>]</span><span class='o'>]</span></span>
<span>    <span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='nv'>movies</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

Now how do I add each element of the list to the original table? I could type something like:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'>for</span> <span class='o'>(</span><span class='nv'>info</span> <span class='kr'>in</span> <span class='nv'>info_list</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>movies</span> <span class='o'>&lt;-</span> <span class='nf'>add_element</span><span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>info</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span><span class='nv'>movies</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 2 × 3</span></span></span>
<span><span class='c'>#&gt;   title       color elements                 </span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>       <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>                    </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span> Barbie      pink  shoes, sparkles, feminism</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span> Oppenheimer red   history, fire</span></span>
<span></span></code></pre>

</div>

It's not too bad, really. But since there's another way, we can change it.

## After

With [`purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html)

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'>for</span> <span class='o'>(</span><span class='nv'>var</span> <span class='kr'>in</span> <span class='nv'>variables_vector</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>thing</span> <span class='o'>&lt;-</span> <span class='nf'>do_something</span><span class='o'>(</span><span class='nv'>thing</span>, <span class='nv'>var</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

can become

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>thing</span> <span class='o'>&lt;-</span> <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/reduce.html'>reduce</a></span><span class='o'>(</span><span class='nv'>variables_vector</span>, <span class='nv'>do_something</span>, .init <span class='o'>=</span> <span class='nv'>thing</span><span class='o'>)</span></span></code></pre>

</div>

And (notice the other argument),

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'>for</span> <span class='o'>(</span><span class='nv'>var</span> <span class='kr'>in</span> <span class='nv'>variables_vector</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>thing</span> <span class='o'>&lt;-</span> <span class='nf'>do_something</span><span class='o'>(</span><span class='nv'>thing</span>, <span class='nv'>var</span>, other_argument <span class='o'>=</span> <span class='nv'>other_argument</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

can become

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>thing</span> <span class='o'>&lt;-</span> <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/reduce.html'>reduce</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>variables_vector</span>, </span>
<span>  \<span class='o'>(</span><span class='nv'>thing</span>, <span class='nv'>x</span><span class='o'>)</span> <span class='nf'>do_something</span><span class='o'>(</span><span class='nv'>thing</span>, <span class='nv'>x</span>, other_argument <span class='o'>=</span> <span class='nv'>other_argument</span><span class='o'>)</span>, </span>
<span>  .init <span class='o'>=</span> <span class='nv'>thing</span></span>
<span><span class='o'>)</span></span></code></pre>

</div>

I haven't completely internalized the pattern above but the [documentation of `purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html#arguments) states

> "We now generally recommend against using ... to pass additional (constant) arguments to .f. Instead use a shorthand anonymous function:
>
> Instead of x \|\> map(f, 1, 2, collapse = ",") do: x \|\> map((x) f(x, 1, 2, collapse = ",")) This makes it easier to understand which arguments belong to which function and will tend to yield better error messages."

It might remind you of how things work for [`dplyr::across()`](https://dplyr.tidyverse.org/reference/across.html) [these days](https://mastodon.social/deck/@blasbenito@fosstodon.org/110745684166845628).

### Back to our silly example!

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># Some basic movie information</span></span>
<span><span class='nv'>movies</span> <span class='o'>&lt;-</span> <span class='nf'>tibble</span><span class='nf'>::</span><span class='nf'><a href='https://tibble.tidyverse.org/reference/tribble.html'>tribble</a></span><span class='o'>(</span></span>
<span>  <span class='o'>~</span><span class='nv'>title</span>, <span class='o'>~</span><span class='nv'>color</span>, <span class='o'>~</span><span class='nv'>elements</span>,</span>
<span>  <span class='s'>"Barbie"</span>, <span class='s'>"pink"</span>, <span class='s'>"shoes"</span>,</span>
<span>  <span class='s'>"Oppenheimer"</span>, <span class='s'>"red"</span>, <span class='s'>"history"</span></span>
<span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># More information to add to movies</span></span>
<span><span class='nv'>info_list</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>title <span class='o'>=</span> <span class='s'>"Barbie"</span>, info <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>element <span class='o'>=</span> <span class='s'>"sparkles"</span><span class='o'>)</span><span class='o'>)</span>,</span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>title <span class='o'>=</span> <span class='s'>"Barbie"</span>, info <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>element <span class='o'>=</span> <span class='s'>"feminism"</span><span class='o'>)</span><span class='o'>)</span>,</span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>title <span class='o'>=</span> <span class='s'>"Oppenheimer"</span>, info <span class='o'>=</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>element <span class='o'>=</span> <span class='s'>"fire"</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>add_element</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>info</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>movies</span><span class='o'>[</span><span class='nv'>movies</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>==</span> <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span>,<span class='o'>]</span><span class='o'>[[</span><span class='s'>"elements"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>&lt;-</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/toString.html'>toString</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span></span>
<span>      <span class='nv'>movies</span><span class='o'>[</span><span class='nv'>movies</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>==</span> <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span>,<span class='o'>]</span><span class='o'>[[</span><span class='s'>"elements"</span><span class='o'>]</span><span class='o'>]</span>,</span>
<span>      <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"info"</span><span class='o'>]</span><span class='o'>]</span><span class='o'>[[</span><span class='m'>1</span><span class='o'>]</span><span class='o'>]</span></span>
<span>    <span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='nv'>movies</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/reduce.html'>reduce</a></span><span class='o'>(</span><span class='nv'>info_list</span>, <span class='nv'>add_element</span>, .init <span class='o'>=</span> <span class='nv'>movies</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 2 × 3</span></span></span>
<span><span class='c'>#&gt;   title       color elements                 </span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>       <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>                    </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span> Barbie      pink  shoes, sparkles, feminism</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span> Oppenheimer red   history, fire</span></span>
<span></span></code></pre>

</div>

If we tweak the `add_element()` function to add a `separator` argument to it,

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>add_element</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>info</span>, <span class='nv'>separator</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>movies</span><span class='o'>[</span><span class='nv'>movies</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>==</span> <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span>,<span class='o'>]</span><span class='o'>[[</span><span class='s'>"elements"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>&lt;-</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span></span>
<span>      <span class='nv'>movies</span><span class='o'>[</span><span class='nv'>movies</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>==</span> <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"title"</span><span class='o'>]</span><span class='o'>]</span>,<span class='o'>]</span><span class='o'>[[</span><span class='s'>"elements"</span><span class='o'>]</span><span class='o'>]</span>,</span>
<span>      <span class='nv'>info</span><span class='o'>[[</span><span class='s'>"info"</span><span class='o'>]</span><span class='o'>]</span><span class='o'>[[</span><span class='m'>1</span><span class='o'>]</span><span class='o'>]</span></span>
<span>    <span class='o'>)</span>, collapse <span class='o'>=</span> <span class='nv'>separator</span><span class='o'>)</span></span>
<span>  <span class='nv'>movies</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/reduce.html'>reduce</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>info_list</span>, </span>
<span>  \<span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>x</span><span class='o'>)</span> <span class='nf'>add_element</span><span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>x</span>, separator <span class='o'>=</span> <span class='s'>" - "</span><span class='o'>)</span>, </span>
<span>  .init <span class='o'>=</span> <span class='nv'>movies</span></span>
<span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 2 × 3</span></span></span>
<span><span class='c'>#&gt;   title       color elements                   </span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>       <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>                      </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span> Barbie      pink  shoes - sparkles - feminism</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span> Oppenheimer red   history - fire</span></span>
<span></span><span></span>
<span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/reduce.html'>reduce</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>info_list</span>, </span>
<span>  \<span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>x</span><span class='o'>)</span> <span class='nf'>add_element</span><span class='o'>(</span><span class='nv'>movies</span>, <span class='nv'>x</span>, separator <span class='o'>=</span> <span class='s'>" PLUS "</span><span class='o'>)</span>, </span>
<span>  .init <span class='o'>=</span> <span class='nv'>movies</span></span>
<span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'># A tibble: 2 × 3</span></span></span>
<span><span class='c'>#&gt;   title       color elements                         </span></span>
<span><span class='c'>#&gt;   <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>       <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span> <span style='color: #555555; font-style: italic;'>&lt;chr&gt;</span>                            </span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>1</span> Barbie      pink  shoes PLUS sparkles PLUS feminism</span></span>
<span><span class='c'>#&gt; <span style='color: #555555;'>2</span> Oppenheimer red   history PLUS fire</span></span>
<span></span></code></pre>

</div>

And voilà!

## Conclusion

In this post I presented my approximate understanding of [`purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html), that helped me avoid writing some for loops and instead more elegant code... or at least helped me understand a pattern that in the future I could use elegantly. I can only hope I [`purrr::accumulate()`](https://purrr.tidyverse.org/reference/accumulate.html) more experience, as I very much still feel like a newbie.

For more information I'd recommend reading the documentation of [`purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html) to be aware of other features, [the content on the reduce family in Advanced R by Hadley Wickham](https://adv-r.hadley.nz/functionals.html#reduce)... and release-watching the purrr repo to keep up-to-date with latest recommendations. You can also use GitHub Advanced Search to find examples of usage of the function in, say, [CRAN packages](https://github.com/search?q=purrr%3A%3Areduce+org%3Acran&type=code).

Edit: For another take of / use case of [`purrr::reduce()`](https://purrr.tidyverse.org/reference/reduce.html), June Choe wrote a nice detailed tutorial ["Collapse repetitive piping with reduce()"](https://yjunechoe.github.io/posts/2020-12-13-collapse-repetitive-piping-with-reduce/).

[^1]: The package is [glitter](https://github.com/lvaudor/glitter), where we store query objects as a list.

