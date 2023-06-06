---
title: "Three useful (to me) R patterns"
date: '2023-06-06'
tags:
  - good practice
  - code style
slug: git-github-casual
output: hugodown::hugo_document
rmd_hash: 2b918b159d5f67cb

---

I'm happy to report that I thought "oh but I know a better way to write that code!" a few times lately when reading old scripts of mine, or scripts by others. It's a good feeling because it shows progress! I've tooted about all three things I'll present in this post: After reading Julia Evans' [post about blogging](https://jvns.ca/blog/2023/06/05/some-blogging-myths/), I decided to train the blogging muscle a bit using these low-hanging fruits/toots[^1].

## Combine a list of default values with a list of custom values

[*Toot*](https://mastodon.social/@maelle/110134566950035337)

Imagine 💭

🎚️ you have a `default_values` list and 👇 want to let the user pass a `custom_values` list to override some of them.

✨ `utils::modifyList(default_values, custom_values)`does that!

So say you had code à la

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>default_values</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>a <span class='o'>=</span> <span class='m'>1</span>, b <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span><span class='nv'>options</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>b <span class='o'>=</span> <span class='m'>56</span><span class='o'>)</span></span>
<span><span class='nv'>temporary_list</span> <span class='o'>&lt;-</span> <span class='nv'>default_values</span></span>
<span><span class='nv'>temporary_list</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/names.html'>names</a></span><span class='o'>(</span><span class='nv'>options</span><span class='o'>)</span><span class='o'>]</span> <span class='o'>&lt;-</span> <span class='nv'>options</span></span>
<span><span class='nv'>options</span> <span class='o'>&lt;-</span> <span class='nv'>temporary_list</span></span>
<span></span>
<span><span class='nv'>options</span></span>
<span><span class='c'>#&gt; $a</span></span>
<span><span class='c'>#&gt; [1] 1</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $b</span></span>
<span><span class='c'>#&gt; [1] 56</span></span>
<span></span></code></pre>

</div>

You can write it like so

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>default_values</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>a <span class='o'>=</span> <span class='m'>1</span>, b <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span><span class='nv'>options</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/list.html'>list</a></span><span class='o'>(</span>b <span class='o'>=</span> <span class='m'>56</span><span class='o'>)</span></span>
<span><span class='nv'>options</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/utils/modifyList.html'>modifyList</a></span><span class='o'>(</span><span class='nv'>default_values</span>, <span class='nv'>options</span><span class='o'>)</span></span>
<span><span class='nv'>options</span></span>
<span><span class='c'>#&gt; $a</span></span>
<span><span class='c'>#&gt; [1] 1</span></span>
<span><span class='c'>#&gt; </span></span>
<span><span class='c'>#&gt; $b</span></span>
<span><span class='c'>#&gt; [1] 56</span></span>
<span></span></code></pre>

</div>

I learnt about that function in [pkgdown source](https://github.com/r-lib/pkgdown/blob/c354aa7e5ea1f9936692494c28c89e5bdd31fc68/R/utils.R#L109).

## Use a default if the user provided NULL

[*Toot*](https://mastodon.social/@maelle/110054745129675027)

Do you know the [rlang `%||%` operator](https://rlang.r-lib.org/reference/op-null-default.html)?[^2]

Code like

``` r
if (is.null(blop)) {
  blop <- 42
}
```

can become

``` r
blop <- blop %||% 42
```

Related to this, I'd recommend package developers read the [chapter of the Tidyverse design guide on defaults](https://design.tidyverse.org/def-short.html), especially the section on the [`NULL` default](https://design.tidyverse.org/def-short.html#arg-short-null).

## Extract common values or different / unique values from two vectors

[*Toot*](https://mastodon.social/@maelle/110474929146978928)

Say I have a vector a and a vector b, and I need the unique a values that are not in b.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>a</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"thing"</span>, <span class='s'>"object"</span><span class='o'>)</span></span>
<span><span class='nv'>b</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"thing"</span>, <span class='s'>"gift"</span><span class='o'>)</span></span></code></pre>

</div>

I tended to write something like

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/unique.html'>unique</a></span><span class='o'>(</span><span class='nv'>a</span><span class='o'>[</span><span class='o'>!</span><span class='o'>(</span><span class='nv'>a</span> <span class='o'><a href='https://rdrr.io/r/base/match.html'>%in%</a></span> <span class='nv'>b</span><span class='o'>)</span><span class='o'>]</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "object"</span></span>
<span></span></code></pre>

</div>

(or without the [`unique()`](https://rdrr.io/r/base/unique.html) if a has only distinct values)

that can be

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/sets.html'>setdiff</a></span><span class='o'>(</span><span class='nv'>a</span>, <span class='nv'>b</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "object"</span></span>
<span></span></code></pre>

</div>

Similarly, when looking for the unique values of the two vectors combined, instead of

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/unique.html'>unique</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='nv'>a</span>, <span class='nv'>b</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "thing"  "object" "gift"</span></span>
<span></span></code></pre>

</div>

I can write

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/sets.html'>union</a></span><span class='o'>(</span><span class='nv'>a</span>, <span class='nv'>b</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "thing"  "object" "gift"</span></span>
<span></span></code></pre>

</div>

Because I've noticed I didn't know these base R functions well enough, I open the Set Operations manual page more often, by typing [`?setdiff`](https://rdrr.io/r/base/sets.html) for instance.

Salix Dubois helpfully noted the functions can be [slower](https://mastodon.social/@salixdubois@zeroes.ca/110477215577045367), and that one might not always want to drop duplicates.

## Conclusion

In this post I presented three basic (set of) functions (not all *base* functions) that I've found serve me well: [`utils::modifyList()`](https://rdrr.io/r/utils/modifyList.html), `rlang::%||%` and base Set Operations. I'm glad they're now part of my R vocabulary.

Note that you might still prefer the longer version of some of these patterns, depending on your needs, your code readers, etc. I won't judge!

I'm curious to see what three new things I'll have learnt in a few months (and will try not to beat myself up for not learning about them sooner :innocent:). If you're interested about code quality in general, you might enjoy this [post by Christophe Dervieux and myself on the R-hub blog](https://blog.r-hub.io/2022/03/21/code-style/).

[^1]: I hope it's a myth that your puns need to be good!

[^2]: Please don't ask me to say this aloud, I have no idea how it's pronounced.

