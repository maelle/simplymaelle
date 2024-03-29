---
title: "Why I like XPath, XML and HTML"
date: '2022-04-08'
tags:
  - XML
  - xml2
  - XPath
slug: xml-xpath
output: hugodown::hugo_document
rmd_hash: 04da9e49ce8671d4

---

One of my favorite tool is XPath, the query language for exploring XML and HTML trees. In this post, I will highlight a few use cases of this ["angle-bracket crunching tool"](https://github.com/hrbrmstr/htmltidy#htmltidy=) and hope to convince you that it's an awesome thing to know about and play with.

*Many thanks to [Christophe Dervieux](https://cderv.rbind.io/) for useful feedback on this post! Mille mercis !*

## Brief intro to XPath in R

Say I have some XML,

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>my_xml</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_xml</a></span><span class='o'>(</span><span class='s'>"&lt;wrapper&gt;&lt;thing&gt;blop&lt;/thing&gt;&lt;/wrapper&gt;"</span><span class='o'>)</span></code></pre>

</div>

I'm using [xml2](https://xml2.r-lib.org/), by Hadley Wickham, Jim Hester and Jeroen Ooms. This package is recommended over the XML package by e.g. the [rOpenSci dev guide](https://devguide.ropensci.org/building.html#recommended-scaffolding).

With XPath I can query the "thing" element:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_all</a></span><span class='o'>(</span><span class='nv'>my_xml</span>, <span class='s'>".//thing"</span><span class='o'>)</span>
<span class='c'>#&gt; &#123;xml_nodeset (1)&#125;</span>
<span class='c'>#&gt; [1] &lt;thing&gt;blop&lt;/thing&gt;</span></code></pre>

</div>

I can extract its content via [`xml2::xml_text()`](http://xml2.r-lib.org/reference/xml_text.html):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_all</a></span><span class='o'>(</span><span class='nv'>my_xml</span>, <span class='s'>".//thing"</span><span class='o'>)</span> |&gt;
  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='o'>)</span>
<span class='c'>#&gt; [1] "blop"</span></code></pre>

</div>

I could also [replace the element](https://blog.r-hub.io/2020/01/22/mutable-api/#exposing-the-c-api-in-xml2).

Now, that was an especially simple XPath query. XPath's strength is to allow you to really take advantage of the structure of the XML or HTML tree. You can extract nodes based on their attributes, on their parents, on their siblings, etc.

Where to learn XPath? Two good websites to get started are

-   [Mozilla Developer Network's intro to XPath](https://developer.mozilla.org/en-US/docs/Web/XPath);
-   [w3schools' XPath tutorial](https://www.w3schools.com/xml/xpath_intro.asp).

A primary skill to learn is the name of elements, e.g. nodes, attributes, which will help write type better keywords into search engines when trying to figure out a query. :wink:

Note that if you are handling HTML, you might enjoy [selectr by Simon Potter](https://sjp.co.nz/projects/selectr/) that creates XPath filters based on CSS selectors.

Knowing XPath, or even knowing it exists, is really empowering. In the rest of this post, I'll highlight cases where this is useful.

## When life gives you XML or HTML

### Web scraping

At the beginning of this blog I liked extracting data from websites. I did that with [regular expressions](/2017/03/07/blinddates/). Now I know better and would [wrangle HTML as HTML](/2021/01/15/beanie-baby/). Goodbye, [`stringr::str_detect()`](https://stringr.tidyverse.org/reference/str_detect.html), hello, [`xml2::xml_find_all()`](http://xml2.r-lib.org/reference/xml_find_all.html).

A package that's especially useful for web scraping is [rvest by Hadley Wickham](https://rvest.tidyverse.org/). rvest builds upon selectr, and will write XPath for you.

### pkgdown

If you use pkgdown to produce the documentation website of for your package, please know that part of its magic comes from various "HTML tweaks" that are powered by XPath, see for instance ["tweak-reference.R"](https://github.com/r-lib/pkgdown/blob/98d5a5c735eb244cb98b2e6bab1d54bb27c0af95/R/tweak-reference.R#L1).

## When life gives you something else...

You can still make it XML to handle it as such, with XPath!

### Markdown manipulation with commonmark, tinkr

The commonmark package transforms Markdown to XML. This can be extremely handy to get data [on R Markdown or Markdown files](https://ropensci.org/blog/2018/09/05/commonmark/).

Now, say you want to modify the Markdown file as XML then get a Markdown file back. It is also possible, with the [tinkr package](https://docs.ropensci.org/tinkr/), started by yours truly, now maintained by Zhian Kamvar. The conversion back to Markdown relies on [xslt](https://docs.ropensci.org/xslt/) by Jeroen Ooms, a package that can use XSL stylesheets.

### Code tree manipulation with xmlparsedata

Imagine you're writing a domain-specific language where you let users write something like

``` r
str_detect(str_to_lower(itemTitle), 'wikidata')
```

that you want to somehow translate to:

``` sparql
REGEX(LCASE(?itemTitle),"wikidata")
```

Yes, that's a real use case, from the [glitter package (SPARQL DSL) maintained by Lise Vaudor](https://lvaudor.github.io/glitter/articles/internals.html).

The way we translate the code is to transform it to an XML tree via [Gábor Csárdi's xmlparsedata](https://r-lib.github.io/xmlparsedata/), then we can apply different tweaks based on XPath.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nf'><a href='https://rdrr.io/r/base/parse.html'>parse</a></span><span class='o'>(</span>
  text <span class='o'>=</span> <span class='s'>"str_detect(str_to_lower(itemTitle), 'wikidata')"</span>,
  keep.source <span class='o'>=</span> <span class='kc'>TRUE</span>
<span class='o'>)</span> |&gt; 
  <span class='nf'>xmlparsedata</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/xmlparsedata/man/xml_parse_data.html'>xml_parse_data</a></span><span class='o'>(</span>pretty <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> |&gt; 
  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_xml</a></span><span class='o'>(</span><span class='o'>)</span> |&gt;
  <span class='nf'><a href='https://rdrr.io/r/base/character.html'>as.character</a></span><span class='o'>(</span><span class='o'>)</span> |&gt;
  <span class='nf'><a href='https://rdrr.io/r/base/cat.html'>cat</a></span><span class='o'>(</span><span class='o'>)</span>
<span class='c'>#&gt; &lt;?xml version="1.0" encoding="UTF-8" standalone="yes"?&gt;</span>
<span class='c'>#&gt; &lt;exprlist&gt;</span>
<span class='c'>#&gt;   &lt;expr line1="1" col1="1" line2="1" col2="47" start="49" end="95"&gt;</span>
<span class='c'>#&gt;     &lt;expr line1="1" col1="1" line2="1" col2="10" start="49" end="58"&gt;</span>
<span class='c'>#&gt;       &lt;SYMBOL_FUNCTION_CALL line1="1" col1="1" line2="1" col2="10" start="49" end="58"&gt;str_detect&lt;/SYMBOL_FUNCTION_CALL&gt;</span>
<span class='c'>#&gt;     &lt;/expr&gt;</span>
<span class='c'>#&gt;     &lt;OP-LEFT-PAREN line1="1" col1="11" line2="1" col2="11" start="59" end="59"&gt;(&lt;/OP-LEFT-PAREN&gt;</span>
<span class='c'>#&gt;     &lt;expr line1="1" col1="12" line2="1" col2="34" start="60" end="82"&gt;</span>
<span class='c'>#&gt;       &lt;expr line1="1" col1="12" line2="1" col2="23" start="60" end="71"&gt;</span>
<span class='c'>#&gt;         &lt;SYMBOL_FUNCTION_CALL line1="1" col1="12" line2="1" col2="23" start="60" end="71"&gt;str_to_lower&lt;/SYMBOL_FUNCTION_CALL&gt;</span>
<span class='c'>#&gt;       &lt;/expr&gt;</span>
<span class='c'>#&gt;       &lt;OP-LEFT-PAREN line1="1" col1="24" line2="1" col2="24" start="72" end="72"&gt;(&lt;/OP-LEFT-PAREN&gt;</span>
<span class='c'>#&gt;       &lt;expr line1="1" col1="25" line2="1" col2="33" start="73" end="81"&gt;</span>
<span class='c'>#&gt;         &lt;SYMBOL line1="1" col1="25" line2="1" col2="33" start="73" end="81"&gt;itemTitle&lt;/SYMBOL&gt;</span>
<span class='c'>#&gt;       &lt;/expr&gt;</span>
<span class='c'>#&gt;       &lt;OP-RIGHT-PAREN line1="1" col1="34" line2="1" col2="34" start="82" end="82"&gt;)&lt;/OP-RIGHT-PAREN&gt;</span>
<span class='c'>#&gt;     &lt;/expr&gt;</span>
<span class='c'>#&gt;     &lt;OP-COMMA line1="1" col1="35" line2="1" col2="35" start="83" end="83"&gt;,&lt;/OP-COMMA&gt;</span>
<span class='c'>#&gt;     &lt;expr line1="1" col1="37" line2="1" col2="46" start="85" end="94"&gt;</span>
<span class='c'>#&gt;       &lt;STR_CONST line1="1" col1="37" line2="1" col2="46" start="85" end="94"&gt;'wikidata'&lt;/STR_CONST&gt;</span>
<span class='c'>#&gt;     &lt;/expr&gt;</span>
<span class='c'>#&gt;     &lt;OP-RIGHT-PAREN line1="1" col1="47" line2="1" col2="47" start="95" end="95"&gt;)&lt;/OP-RIGHT-PAREN&gt;</span>
<span class='c'>#&gt;   &lt;/expr&gt;</span>
<span class='c'>#&gt; &lt;/exprlist&gt;</span></code></pre>

</div>

To me, having an XML tree at hand makes it easier to think of, and work with, an "abstract syntax tree".

### XPath for all the things

A tool that I haven't used, but that sounds intriguing, is [rpath by Gabriel Becker](https://twitter.com/groundwalkergmb/status/1502095683788042240), an *R package implementing xpath-like functionality for querying R objects*.

### Data documentation with EML

No matter what format your data is, you can create its metadata using the [EML package](https://docs.ropensci.org/EML/) maintained by Carl Boettiger that creates XML metadata following the Ecological Metadata Language. Sure, you might prefer using [dataspice](https://docs.ropensci.org/dataspice/) maintained by Bryce Mecum (and get JSON).

## When you are creating XML or HTML

If the goal of your code or package is to produce XML or HTML, knowing XPath will help you write unit tests (that you might want to complement with [snapshot unit tests](https://testthat.r-lib.org/articles/snapshotting.html)).

## Conclusion

In this post I've explained why I find XPath, XML, HTML so useful. Applications are endless, not limited to the examples from this post: web scraping, HTML tweaks, Markdown manipulation, code tree manipulation...

