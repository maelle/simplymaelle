---
title: "3 R functions that I enjoy"
date: '2023-09-29'
slug: three-shorten
output: hugodown::hugo_document
rmd_hash: 7d258f1ab9d77f3d

---

Straight from my sticky note, three functions that I like a lot, despite their not being new at all... But maybe new to some of you?

## `sprintf()`, the dependency-free but less neat "glue"

Imagine I want to tell you who I am.

I could write

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>name</span> <span class='o'>&lt;-</span> <span class='nf'>whoami</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/whoami/man/fullname.html'>fullname</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nv'>github_username</span> <span class='o'>&lt;-</span> <span class='nf'>whoami</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/whoami/man/gh_username.html'>gh_username</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nf'>glue</span><span class='nf'>::</span><span class='nf'><a href='https://glue.tidyverse.org/reference/glue.html'>glue</a></span><span class='o'>(</span><span class='s'>"My name is &#123;name&#125; and you'll find me on GitHub as &#123;github_username&#125;!"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; My name is Maëlle Salmon and you'll find me on GitHub as maelle!</span></span>
<span></span></code></pre>

</div>

(Maybe the [whoami](https://r-lib.github.io/whoami/) package by Gábor Csárdi is a sub topic of this section?! So handy!)

The code above is very readable. The nice syntax with curly braces is something one finds again in the [cli package](https://cli.r-lib.org/).[^1]

Now, there's a dependency-free version of the glue code! Albeit a bit uglier 🤐

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>name</span> <span class='o'>&lt;-</span> <span class='nf'>whoami</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/whoami/man/fullname.html'>fullname</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nv'>github_username</span> <span class='o'>&lt;-</span> <span class='nf'>whoami</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/whoami/man/gh_username.html'>gh_username</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/sprintf.html'>sprintf</a></span><span class='o'>(</span></span>
<span>  <span class='s'>"My name is %s and you'll find me on GitHub as %s!"</span>,</span>
<span>  <span class='nv'>name</span>,</span>
<span>  <span class='nv'>github_username</span></span>
<span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "My name is Maëlle Salmon and you'll find me on GitHub as maelle!"</span></span>
<span></span></code></pre>

</div>

Sure it's less readable, since the replacements are identified by their position, but I often find myself using it! I clearly remember seeing it in other people's code and wondering what that was.

It's a pattern one finds in other languages: the manual page for the function states "Use C-style String Formatting Commands", and I know the [Hugo equivalent](https://gohugo.io/functions/printf/).

## `append()` and its after argument

To append values to a vector, I mostly use [`c()`](https://rdrr.io/r/base/c.html), but I recently discovered the base R function [`append()`](https://rdrr.io/r/base/append.html) and its `after` argument that indicates where to include the new values! By default, the values are appended at the end of the vector.

I most recently used [`append()`](https://rdrr.io/r/base/append.html) to create a [test fixture](https://github.com/ropensci-review-tools/babeldown/blob/8e0fe9626c8ebe7cb70839b7751dfa803789107a/tests/testthat/test-translate-hugo.R#L12).

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"thing"</span>, <span class='s'>"stuff"</span>, <span class='s'>"element"</span><span class='o'>)</span></span>
<span><span class='nv'>values</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span><span class='s'>"bla"</span>, <span class='s'>"blop"</span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/base/append.html'>append</a></span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>values</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "thing"   "stuff"   "element" "bla"     "blop"</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/append.html'>append</a></span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>values</span>, after <span class='o'>=</span> <span class='m'>2</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "thing"   "stuff"   "bla"     "blop"    "element"</span></span>
<span></span></code></pre>

</div>

It's not a function I use every day, but it can come in handy depending on what you're doing!

For my fellow XML fans out there, it reminds me of the `.where` argument of [`xml2::xml_add_sibling()`](http://xml2.r-lib.org/reference/xml_replace.html) and [`xml2::xml_add_child()`](http://xml2.r-lib.org/reference/xml_replace.html).

## `servr::httw()` to serve a local directory as a website

Do you know about the [servr package by Yihui Xie](https://github.com/yihui/servr)?

Its use case is having a local directory that is the source of a website, and wanting to preview it locally in your browser as if it were served by a real server.

I make use of it when working on the babelquarto package for instance, that builds multilingual Quarto books or websites. In the code of the multilingual books/websites, links to the other language versions are relative so they don't work if I simply open HTML files in my browser. So, instead, I write `servr::httw(<directory-with-the-website-source>)`.

You can also use servr if you want to preview locally your pkgdown website and be able to use the [search function](https://pkgdown.r-lib.org/articles/search.html#bootstrap-5-built-in-search).

There's [`servr::httw()`](https://rdrr.io/pkg/servr/man/httd.html) that watches changes in the directory, and the more simple [`servr::httd()`](https://rdrr.io/pkg/servr/man/httd.html).

## Conclusion

Today I shared about [`sprintf()`](https://rdrr.io/r/base/sprintf.html) for glue-like functionality, [`append()`](https://rdrr.io/r/base/append.html) and its `after` argument for appending values where you want in a vector, and [`servr::httw()`](https://rdrr.io/pkg/servr/man/httd.html) for serving static files.

[^1]: Curious about cli? Come to this cool [rOpenSci coworkign session next week](https://ropensci.org/events/coworking-2023-10/)!

