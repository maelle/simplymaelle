---
title: "Create and use a custom roxygen2 tag"
date: '2024-09-03'
slug: roxygen2-custom-tag
output: hugodown::hugo_document
tags:
  - package development
  - roxygen2
rmd_hash: e7ca9589a2b6f963

---

*This post was featured on the [R Weekly highlights podcast](https://serve.podhome.fm/episodepage/r-weekly-highlights/178) hosted by Eric Nantz and Mike Thomas.*

You might know that it's possible to extend [roxygen2](https://roxygen2.r-lib.org/) to do all sorts of cool things including but not limited to:

-   documenting your internal functions for developers only (that's [devtag by my cynkra colleague Antoine Fabri](https://github.com/moodymudskipper/devtag)),
-   recording your following statistical software standards (that's [srr by my rOpenSci colleague Mark Padgham](https://github.com/ropensci-review-tools/srr)),
-   writing tests from within R scripts (that's [roxytest by Mikkel Meyer Andersen](https://github.com/mikldk/roxytest)).

I did something much more basic but still challenging to me: creating a custom tag that adds a new section to a manual page. In this post I'll summarize how to go about that.

## Context: need for a custom tag in the igraph R package

The igraph R package outsources a lot of its work to the igraph C library. Sometimes, the R package docs therefore have to try and catch up with the C library docs. One idea we've had when thinking about the problem is to link to relevant C docs from each R manual page: if a function called [`igraph::empty_graph()`](https://r.igraph.org/reference/make_empty_graph.html) uses `igraph_empty` under the hood, then its manual page should link to <https://igraph.org/c/doc/igraph-Basic.html#igraph_empty>. It means the users would be able to easily browse the C docs, and as a side-effect they'd be aware of what part of the C library supports what functionality in the R package.

A workflow to make that happen is the following:

-   have a sitemap of the C docs at hand (we [parse the index page of the C docs](https://github.com/igraph/igraph.r2cdocs/blob/main/data-raw/c-links.R));
-   add `@cdocs` tags in the source of the R package, for instance `@cdocs igraph_empty` near the function definition (adding them semi-automatically will be a topic for another day :sweat_smile:);
-   have **some roxygen2 machinery parsing the `@cdocs`** into links in the manual page when we call `document()`.

This blog post is about the roxygen2 machinery as I had to piece together several things I saw online.

## Minimal example

### Create our minimal R package

[R package hgb](https://github.com/maelle/hgb)

The hgb package has one script whose docs use a custom `@custom` tag:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#' A function messaging a thing</span></span>
<span><span class='c'>#'</span></span>
<span><span class='c'>#' @custom Hello I am here</span></span>
<span><span class='c'>#'</span></span>
<span><span class='c'>#' @return Nothing, print thing</span></span>
<span><span class='c'>#' @export</span></span>
<span><span class='c'>#'</span></span>
<span><span class='c'>#' @examples</span></span>
<span><span class='c'>#' thing()</span></span>
<span><span class='nv'>thing</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/message.html'>message</a></span><span class='o'>(</span><span class='s'>"thing"</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

### Create a package holding the custom tag

[R package myroxy](https://github.com/maelle/myroxy)

In a script I have the following lines that mostly come from an [roxygen2 vignette](https://roxygen2.r-lib.org/articles/extending.html), in which the creation of a custom tag is well documented (its usage less so :sweat_smile:).

There's a method `roxy_tag_parse.roxy_tag_custom()` telling roxygen2 how to parse the tag, a method `roxy_tag_rd.roxy_tag_custom` telling roxygen2 what to do with the value: a special "custom" section. Then there's a method `format.rd_section_custom()` telling roxygen2 how to build the "custom" section. Here it's just pasting. In my real use case I defined a helper function to identify the C docs link corresponding to a given tag value.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#' @importFrom roxygen2 roxy_tag_parse</span></span>
<span><span class='c'>#' @importFrom roxygen2 roxy_tag_rd</span></span>
<span><span class='kc'>NULL</span></span>
<span></span>
<span><span class='c'>#' @export</span></span>
<span><span class='c'># same as https://roxygen2.r-lib.org/articles/extending.html</span></span>
<span><span class='nv'>roxy_tag_parse.roxy_tag_custom</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'>roxygen2</span><span class='nf'>::</span><span class='nf'><a href='https://roxygen2.r-lib.org/reference/tag_parsers.html'>tag_markdown</a></span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='c'>#' @export</span></span>
<span><span class='c'># same as https://roxygen2.r-lib.org/articles/extending.html</span></span>
<span><span class='nv'>roxy_tag_rd.roxy_tag_custom</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>base_path</span>, <span class='nv'>env</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'>roxygen2</span><span class='nf'>::</span><span class='nf'><a href='https://roxygen2.r-lib.org/reference/rd_section.html'>rd_section</a></span><span class='o'>(</span><span class='s'>"custom"</span>, <span class='nv'>x</span><span class='o'>$</span><span class='nv'>val</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='c'>#' @export</span></span>
<span><span class='c'># same as https://roxygen2.r-lib.org/articles/extending.html</span></span>
<span><span class='nv'>format.rd_section_custom</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>...</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste0</a></span><span class='o'>(</span></span>
<span>    <span class='s'>"\\section&#123;Custom section&#125;&#123;\n"</span>,</span>
<span>    <span class='s'>"\\itemize&#123;\n"</span>,</span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste0</a></span><span class='o'>(</span><span class='s'>"  \\item "</span>, <span class='nv'>x</span><span class='o'>$</span><span class='nv'>value</span>, <span class='s'>"\n"</span>, collapse <span class='o'>=</span> <span class='s'>""</span><span class='o'>)</span>,</span>
<span>    <span class='s'>"&#125;\n"</span>,</span>
<span>    <span class='s'>"&#125;\n"</span></span>
<span>  <span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span></span></code></pre>

</div>

### Create a roclet for sharing the custom tag

Even if all we do is creating a custom roxygen2 tag, we need to define a roclet as it'll be the vessel by which another package can use the custom tag. You can have a custom tag defined inside a package without a roclet if you use the custom tag *within the same package*. But to cross package boundaries you need a roclet.

Further in the same script I have those lines:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'>#' @export</span></span>
<span><span class='c'># https://github.com/shahronak47/informationtag</span></span>
<span><span class='nv'>custom_roclet</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'>roxygen2</span><span class='nf'>::</span><span class='nf'><a href='https://roxygen2.r-lib.org/reference/roclet.html'>roclet</a></span><span class='o'>(</span><span class='s'>"custom"</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='c'>#' @importFrom roxygen2 block_get_tags roclet_process</span></span>
<span><span class='c'>#' @method roclet_process roclet_custom</span></span>
<span><span class='c'>#' @export</span></span>
<span><span class='c'># https://github.com/shahronak47/informationtag</span></span>
<span><span class='nv'>roclet_process.roclet_custom</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>blocks</span>, <span class='nv'>env</span>, <span class='nv'>base_path</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>x</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span></span>
<span><span class='c'>#' @export</span></span>
<span><span class='c'>#' @importFrom roxygen2 block_get_tags roclet_output</span></span>
<span><span class='nv'>roclet_output.roclet_custom</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span>, <span class='nv'>results</span>, <span class='nv'>base_path</span>, <span class='nv'>...</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>x</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

You'll notice we defined something called "custom_roclet" but now the methods are for "roclet_custom" (inverted word order). This confused me at first. Furthermore, I had no idea what the process and output methods were supposed to do, so I used the [demo by Ronak Shah](https://github.com/shahronak47/informationtag). Lastly, the roxygen2 tags used in that demo didn't work for me so I stole the ones used by Antoine Fabri in [devtag](https://github.com/moodymudskipper/devtag/blob/61098a12545b34c3bba4e516f29c71da56a7d49d/R/roxygen.R#L20).

### Build the package holding the custom tag

By that I mean running `document()` and installing the myroxy package on my machine to try it.

### Register the roclet in our minimal package

I added the following lines to the `DESCRIPTION` of the hgb package to indicate to roxygen2 that there's another package to use when documenting my package:

``` yaml
Roxygen: list(markdown = TRUE, roclets = c("collate", "rd", "namespace",
    "myroxy::custom_roclet"), packages = "myroxy")
```

I also added these lines so that fellow developers might know where to get myroxy from[^1]:

``` yaml
Config/Needs/build: roxygen2, devtools, maelle/myroxy
```

### Try it

If all goes well, running `document()` in the hgb package works without any error. The manual page for `?thing` has the [custom section](https://github.com/maelle/hgb/blob/c75682742bcaa29efa74f8a7ee055c65640b0aa0/man/thing.Rd#L18).

## Do I need an external package?

The roxygen2 extensions I mentioned in the introduction (devtag, srr, roxytag) are standalone packages that you can use when building your own package, because their use case is fairly general. Here, I am developing a custom tag for igraph only. So in theory, the infrastructure could live inside the R package itself.

I actually started by implementing the custom tag within the igraph package but I was not a fan of the clutter it created in `data-raw`, `R`, the `NAMESPACE`, etc. It was great to start this way as it was much easier (no roclet!), but I ended up factoring out the roxygen2 code in a distinct GitHub-only package called `igraph.r2cdocs`.

## Sources

I am very thankful to the authors of roxygen2 obviously, and to the authors of the following resources:

-   [roxygen2 vignette "Extending roxygen2"](https://roxygen2.r-lib.org/articles/extending.html) that says of itself that it's "very rough". For me the missing part was understanding what goes in what package (the package where you define the tags, the package where you use the tags); and I'd have obviously liked some explanations on roclets that are just there for a basic custom tag, as opposed to printing content / saving information in other files. I want docs just for me. :wink:
-   [Stack Overflow post](https://stackoverflow.com/questions/77865500/how-can-i-create-custom-roxygen2-tags-for-a-package)
-   [Video](https://www.youtube.com/watch?v=AcibRDNSfoM) by Ronak Shah that I didn't watch and related repositories that I did stare at a lot: [informationtag](https://github.com/shahronak47/informationtag) and [infotagdemo](https://github.com/shahronak47/infotagdemo). This is how I understood what the "process" and "output" methods of my roclet had to look like.
-   [devtag source](https://github.com/moodymudskipper/devtag/blob/61098a12545b34c3bba4e516f29c71da56a7d49d/R/roxygen.R#L17) which is where I got the necessary roxygen2 tags for the "process" and "output" methods of my roclet (the ones from the aforementioned demo didn't work for me).

## Conclusion

In this post I explained the creation and usage of a custom roxygen2 tag, with a minimal example. If you're curious about the igraph use case, find the [demo PR to igraph](https://github.com/igraph/rigraph/pull/1484/files) and the [igraph.r2cdocs package](https://github.com/igraph/igraph.r2cdocs/). Have you ever extended roxygen2? What were the difficulties in your experience?

[^1]: This is a [custom... `DESCRIPTION` field](https://r-pkgs.org/description.html#sec-description-custom-fields).

