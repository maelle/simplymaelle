---
title: "Automate code refactoring with {xmlparsedata} and {brio}"
date: '2024-05-15'
slug: refactoring-xml
output: hugodown::hugo_document
tags:
  - xml
  - refactoring
rmd_hash: 68ce0b4d048cec7f

---

Once again a post [praising XML](/2022/04/08/xml-xpath/). :innocent: These are notes from a quite particular use case: what if you want to replace the usage of a function with another one in many scripts, without manual edits and without touching lines that do not contain a call to replace?

The real life example that inspired this post is the replacement of all calls to `expect_that(..., equals(...))`, like `expect_that(a, equals(1))`, in igraph tests with `expect_equal()`. If you're a newer package developer who grew up with testthat's third edition, you've probably never heard of that [cutesy old-school testing style](https://testthat.r-lib.org/reference/oldskool.html). :wink:

## Why automate? Where I subjectively justify my choice

As brilliantly explained by [XKCD 1205](https://xkcd.com/1205/), automation is not necessary worth the time. In the case that motivated this post, automation was worth it because there were many test files, and because being able to regenerate all edits meant we can recreate the changes after merging other edits to the main branch, without any conflict.

## Parse the code to XML, detect problematic calls

For any `path`, we detect function calls to `expect_that()`. The code is parsed using the [`parse()`](https://rdrr.io/r/base/parse.html) function, digested into XML with [{xmlparsedata}](https://github.com/r-lib/xmlparsedata).

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>xml</span> <span class='o'>&lt;-</span> <span class='nv'>path</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/parse.html'>parse</a></span><span class='o'>(</span>keep.source <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'>xmlparsedata</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/xmlparsedata/man/xml_parse_data.html'>xml_parse_data</a></span><span class='o'>(</span>pretty <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_xml</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>deprecated</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_all</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>xml</span>,</span>
<span>  <span class='s'>".//SYMBOL_FUNCTION_CALL[text()='expect_that']"</span></span>
<span><span class='o'>)</span></span></code></pre>

</div>

The `deprecated` object contains all the nodes we need to amend.

For info, here's how code parsed to XML looks like (yes, it is big despite representing two short lines of code):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/parse.html'>parse</a></span><span class='o'>(</span>text <span class='o'>=</span> <span class='s'>"1+1\nsum(c(2,2))"</span>, keep.source <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'>xmlparsedata</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/xmlparsedata/man/xml_parse_data.html'>xml_parse_data</a></span><span class='o'>(</span>pretty <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/cat.html'>cat</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; &lt;?xml version="1.0" encoding="UTF-8" standalone="yes" ?&gt;</span></span>
<span><span class='c'>#&gt; &lt;exprlist&gt;</span></span>
<span><span class='c'>#&gt;   &lt;expr line1="1" col1="1" line2="1" col2="3" start="13" end="15"&gt;</span></span>
<span><span class='c'>#&gt;     &lt;expr line1="1" col1="1" line2="1" col2="1" start="13" end="13"&gt;</span></span>
<span><span class='c'>#&gt;       &lt;NUM_CONST line1="1" col1="1" line2="1" col2="1" start="13" end="13"&gt;1&lt;/NUM_CONST&gt;</span></span>
<span><span class='c'>#&gt;     &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;     &lt;OP-PLUS line1="1" col1="2" line2="1" col2="2" start="14" end="14"&gt;+&lt;/OP-PLUS&gt;</span></span>
<span><span class='c'>#&gt;     &lt;expr line1="1" col1="3" line2="1" col2="3" start="15" end="15"&gt;</span></span>
<span><span class='c'>#&gt;       &lt;NUM_CONST line1="1" col1="3" line2="1" col2="3" start="15" end="15"&gt;1&lt;/NUM_CONST&gt;</span></span>
<span><span class='c'>#&gt;     &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;   &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;   &lt;expr line1="2" col1="1" line2="2" col2="11" start="25" end="35"&gt;</span></span>
<span><span class='c'>#&gt;     &lt;expr line1="2" col1="1" line2="2" col2="3" start="25" end="27"&gt;</span></span>
<span><span class='c'>#&gt;       &lt;SYMBOL_FUNCTION_CALL line1="2" col1="1" line2="2" col2="3" start="25" end="27"&gt;sum&lt;/SYMBOL_FUNCTION_CALL&gt;</span></span>
<span><span class='c'>#&gt;     &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;     &lt;OP-LEFT-PAREN line1="2" col1="4" line2="2" col2="4" start="28" end="28"&gt;(&lt;/OP-LEFT-PAREN&gt;</span></span>
<span><span class='c'>#&gt;     &lt;expr line1="2" col1="5" line2="2" col2="10" start="29" end="34"&gt;</span></span>
<span><span class='c'>#&gt;       &lt;expr line1="2" col1="5" line2="2" col2="5" start="29" end="29"&gt;</span></span>
<span><span class='c'>#&gt;         &lt;SYMBOL_FUNCTION_CALL line1="2" col1="5" line2="2" col2="5" start="29" end="29"&gt;c&lt;/SYMBOL_FUNCTION_CALL&gt;</span></span>
<span><span class='c'>#&gt;       &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;       &lt;OP-LEFT-PAREN line1="2" col1="6" line2="2" col2="6" start="30" end="30"&gt;(&lt;/OP-LEFT-PAREN&gt;</span></span>
<span><span class='c'>#&gt;       &lt;expr line1="2" col1="7" line2="2" col2="7" start="31" end="31"&gt;</span></span>
<span><span class='c'>#&gt;         &lt;NUM_CONST line1="2" col1="7" line2="2" col2="7" start="31" end="31"&gt;2&lt;/NUM_CONST&gt;</span></span>
<span><span class='c'>#&gt;       &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;       &lt;OP-COMMA line1="2" col1="8" line2="2" col2="8" start="32" end="32"&gt;,&lt;/OP-COMMA&gt;</span></span>
<span><span class='c'>#&gt;       &lt;expr line1="2" col1="9" line2="2" col2="9" start="33" end="33"&gt;</span></span>
<span><span class='c'>#&gt;         &lt;NUM_CONST line1="2" col1="9" line2="2" col2="9" start="33" end="33"&gt;2&lt;/NUM_CONST&gt;</span></span>
<span><span class='c'>#&gt;       &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;       &lt;OP-RIGHT-PAREN line1="2" col1="10" line2="2" col2="10" start="34" end="34"&gt;)&lt;/OP-RIGHT-PAREN&gt;</span></span>
<span><span class='c'>#&gt;     &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt;     &lt;OP-RIGHT-PAREN line1="2" col1="11" line2="2" col2="11" start="35" end="35"&gt;)&lt;/OP-RIGHT-PAREN&gt;</span></span>
<span><span class='c'>#&gt;   &lt;/expr&gt;</span></span>
<span><span class='c'>#&gt; &lt;/exprlist&gt;</span></span>
<span></span></code></pre>

</div>

## Fix problematic calls in the XML representation

The `treat_deprecated()` function below tries to find a call to `equals()` inside the `expect_equal()`, since we only fix the calls to `expect_that()` that contain `equals()`. We return early for these other cutesy expectations, with a warning so we can go look at the scripts and get an idea of what the calls are. They will have to be fixed with another script, or manually, depending on how many of them there are.

For the calls to `expect_that()` that contain a call to `equals()`, we

-   replace `expect_that()` with `expect_equal()`
-   extract the text inside `equals()` to put it directly as second argument of `expect_equal()`.

Thus `expect_that(a, equals(1))` becomes `expect_equals(a, 1)`.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>treat_deprecated</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>xml</span>, <span class='nv'>path</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>siblings</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_parent</a></span><span class='o'>(</span><span class='nv'>xml</span><span class='o'>)</span> <span class='o'>|&gt;</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_siblings</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span>  <span class='nv'>equal</span> <span class='o'>&lt;-</span> <span class='nv'>siblings</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grepl</a></span><span class='o'>(</span><span class='s'>"equals\\("</span>, <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>siblings</span><span class='o'>)</span><span class='o'>)</span><span class='o'>]</span></span>
<span>  <span class='kr'>if</span> <span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/length.html'>length</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span> <span class='o'>==</span> <span class='m'>0</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>    <span class='nf'>cli</span><span class='nf'>::</span><span class='nf'><a href='https://cli.r-lib.org/reference/cli_alert.html'>cli_alert_warning</a></span><span class='o'>(</span><span class='s'>"WARNING AT &#123;path&#125;."</span><span class='o'>)</span></span>
<span>    <span class='kr'><a href='https://rdrr.io/r/base/function.html'>return</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span>  <span class='o'>&#125;</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>xml</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='s'>"expect_equal"</span></span>
<span>  <span class='nv'>text</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_contents</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span><span class='o'>[[</span><span class='m'>3</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>|&gt;</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_replace.html'>xml_remove</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_contents</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='nv'>text</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

## Serialize XML to character, write back

We only modify the lines of the script that need to be modified, as it will avoid spurious changes but also avoid figuring out how to serialize the whole HTML.

For each call that was edited in XML, we identify the corresponding start and end lines in the original script. Below is again an example of parsing just to show that each expression has attributes called line1 and line2, the start and end lines.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/parse.html'>parse</a></span><span class='o'>(</span>text <span class='o'>=</span> <span class='s'>"1+1\n2+2"</span>, keep.source <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>    <span class='nf'>xmlparsedata</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/xmlparsedata/man/xml_parse_data.html'>xml_parse_data</a></span><span class='o'>(</span>pretty <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>    <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_xml</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; &#123;xml_document&#125;</span></span>
<span><span class='c'>#&gt; &lt;exprlist&gt;</span></span>
<span><span class='c'>#&gt; [1] &lt;expr line1="1" col1="1" line2="1" col2="3" start="5" end="7"&gt;\n  &lt;expr l ...</span></span>
<span><span class='c'>#&gt; [2] &lt;expr line1="2" col1="1" line2="2" col2="3" start="9" end="11"&gt;\n  &lt;expr  ...</span></span>
<span></span></code></pre>

</div>

There are two cases:

-   the start and end line is the same. We replace the corresponding line with the text of the grand-parent node.
-   the start and end lines are different. We loop over them, for each of them we replace the corresponding line with the text of parent and uncles/aunts nodes.

The choice of parents/siblings might seem a bit arbitrary. I made it work by putting a [`browser()`](https://rdrr.io/r/base/browser.html) in my code and figuring out what level of ancestry I had to deal with thanks to random tries. I do not have a particularly good mental model of R code as XML. :wink:

For some reason I wrote for loops in the code below, probably because that's what made sense to me at the time. :shrug:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>lines</span> <span class='o'>&lt;-</span> <span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/read_lines.html'>read_lines</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span></span>
<span><span class='c'># ...</span></span>
<span><span class='c'># code identifying deprecated calls</span></span>
<span><span class='c'># ...</span></span>
<span></span>
<span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/map.html'>walk</a></span><span class='o'>(</span><span class='nv'>deprecated</span>, <span class='nv'>treat_deprecated</span>, path <span class='o'>=</span> <span class='nv'>path</span><span class='o'>)</span></span>
<span></span>
<span><span class='kr'>for</span> <span class='o'>(</span><span class='nv'>deprecated_call</span> <span class='kr'>in</span> <span class='nv'>deprecated</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  </span>
<span>  <span class='nv'>parent</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_parent</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_parent</a></span><span class='o'>(</span><span class='nv'>deprecated_call</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='nv'>line1</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>parent</span>, <span class='s'>"line1"</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='nv'>line2</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>parent</span>, <span class='s'>"line2"</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  </span>
<span>  <span class='kr'>if</span> <span class='o'>(</span><span class='nv'>line1</span> <span class='o'>==</span> <span class='nv'>line2</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>    <span class='nv'>lines</span><span class='o'>[</span><span class='nv'>line1</span><span class='o'>]</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>parent</span><span class='o'>)</span></span>
<span>  <span class='o'>&#125;</span> <span class='kr'>else</span> <span class='o'>&#123;</span></span>
<span>    <span class='kr'>for</span> <span class='o'>(</span><span class='nv'>line</span> <span class='kr'>in</span> <span class='nv'>line1</span><span class='o'>:</span><span class='nv'>line2</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>      <span class='nv'>siblings</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_children</a></span><span class='o'>(</span><span class='nv'>parent</span><span class='o'>)</span></span>
<span>      <span class='nv'>lines</span><span class='o'>[</span><span class='nv'>line</span><span class='o'>]</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste</a></span><span class='o'>(</span></span>
<span>        <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>siblings</span><span class='o'>[</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>siblings</span>, <span class='s'>"line1"</span><span class='o'>)</span> <span class='o'>==</span> <span class='nv'>line</span><span class='o'>]</span><span class='o'>)</span>,</span>
<span>        collapse <span class='o'>=</span> <span class='s'>""</span></span>
<span>      <span class='o'>)</span></span>
<span>    <span class='o'>&#125;</span></span>
<span>  <span class='o'>&#125;</span></span>
<span>  </span>
<span>  </span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='nv'>lines</span>, <span class='nv'>path</span><span class='o'>)</span></span></code></pre>

</div>

After all this, we write the `lines`, modified and not, to the original `path`. It's important to first try this on a script and check the diff.

## Put it all together

Here's the all script, including automatic commit generation.

<div class='highlight'><pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>parse_script</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span></span>
<span>  <span class='nf'>cli</span><span class='nf'>::</span><span class='nf'><a href='https://cli.r-lib.org/reference/cli_alert.html'>cli_alert_info</a></span><span class='o'>(</span><span class='s'>"Refactoring &#123;path&#125;."</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='nv'>lines</span> <span class='o'>&lt;-</span> <span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/read_lines.html'>read_lines</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='nv'>xml</span> <span class='o'>&lt;-</span> <span class='nv'>path</span> <span class='o'>|&gt;</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/parse.html'>parse</a></span><span class='o'>(</span>keep.source <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>    <span class='nf'>xmlparsedata</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/xmlparsedata/man/xml_parse_data.html'>xml_parse_data</a></span><span class='o'>(</span>pretty <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>    <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_xml</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='nv'>deprecated</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_all</a></span><span class='o'>(</span></span>
<span>    <span class='nv'>xml</span>,</span>
<span>    <span class='s'>".//SYMBOL_FUNCTION_CALL[text()='expect_that']"</span></span>
<span>  <span class='o'>)</span></span>
<span></span>
<span>  <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/map.html'>walk</a></span><span class='o'>(</span><span class='nv'>deprecated</span>, <span class='nv'>treat_deprecated</span>, path <span class='o'>=</span> <span class='nv'>path</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='kr'>for</span> <span class='o'>(</span><span class='nv'>deprecated_call</span> <span class='kr'>in</span> <span class='nv'>deprecated</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span></span>
<span>    <span class='nv'>parent</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_parent</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_parent</a></span><span class='o'>(</span><span class='nv'>deprecated_call</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span>    <span class='nv'>line1</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>parent</span>, <span class='s'>"line1"</span><span class='o'>)</span><span class='o'>)</span></span>
<span>    <span class='nv'>line2</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/numeric.html'>as.numeric</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>parent</span>, <span class='s'>"line2"</span><span class='o'>)</span><span class='o'>)</span></span>
<span></span>
<span>    <span class='kr'>if</span> <span class='o'>(</span><span class='nv'>line1</span> <span class='o'>==</span> <span class='nv'>line2</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>      <span class='nv'>lines</span><span class='o'>[</span><span class='nv'>line1</span><span class='o'>]</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>parent</span><span class='o'>)</span></span>
<span>    <span class='o'>&#125;</span> <span class='kr'>else</span> <span class='o'>&#123;</span></span>
<span>      <span class='kr'>for</span> <span class='o'>(</span><span class='nv'>line</span> <span class='kr'>in</span> <span class='nv'>line1</span><span class='o'>:</span><span class='nv'>line2</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>        <span class='nv'>siblings</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_children</a></span><span class='o'>(</span><span class='nv'>parent</span><span class='o'>)</span></span>
<span>        <span class='nv'>lines</span><span class='o'>[</span><span class='nv'>line</span><span class='o'>]</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste</a></span><span class='o'>(</span></span>
<span>          <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>siblings</span><span class='o'>[</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>siblings</span>, <span class='s'>"line1"</span><span class='o'>)</span> <span class='o'>==</span> <span class='nv'>line</span><span class='o'>]</span><span class='o'>)</span>,</span>
<span>          collapse <span class='o'>=</span> <span class='s'>""</span></span>
<span>        <span class='o'>)</span></span>
<span>      <span class='o'>&#125;</span></span>
<span>    <span class='o'>&#125;</span></span>
<span></span>
<span></span>
<span>  <span class='o'>&#125;</span></span>
<span></span>
<span>  <span class='nf'>brio</span><span class='nf'>::</span><span class='nf'><a href='https://brio.r-lib.org/reference/write_lines.html'>write_lines</a></span><span class='o'>(</span><span class='nv'>lines</span>, <span class='nv'>path</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='kr'>if</span> <span class='o'>(</span><span class='o'>!</span> <span class='o'>(</span><span class='nv'>path</span> <span class='o'><a href='https://rdrr.io/r/base/match.html'>%in%</a></span> <span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_status</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>[[</span><span class='s'>"file"</span><span class='o'>]</span><span class='o'>]</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>    <span class='kr'><a href='https://rdrr.io/r/base/function.html'>return</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/invisible.html'>invisible</a></span><span class='o'>(</span><span class='kc'>TRUE</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='o'>&#125;</span></span>
<span></span>
<span>  <span class='nf'>styler</span><span class='nf'>::</span><span class='nf'><a href='https://styler.r-lib.org/reference/style_file.html'>style_file</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span></span>
<span>  <span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_add</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span>  <span class='nf'>gert</span><span class='nf'>::</span><span class='nf'><a href='https://docs.ropensci.org/gert/reference/git_commit.html'>git_commit</a></span><span class='o'>(</span></span>
<span>    <span class='nf'><a href='https://rdrr.io/r/base/sprintf.html'>sprintf</a></span><span class='o'>(</span><span class='s'>"refactor: remove deprecated expect_that() from %s"</span>, <span class='nf'>fs</span><span class='nf'>::</span><span class='nf'><a href='https://fs.r-lib.org/reference/path_file.html'>path_file</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='nv'>treat_deprecated</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>xml</span>, <span class='nv'>path</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>siblings</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_parent</a></span><span class='o'>(</span><span class='nv'>xml</span><span class='o'>)</span> <span class='o'>|&gt;</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_siblings</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span>  <span class='nv'>equal</span> <span class='o'>&lt;-</span> <span class='nv'>siblings</span><span class='o'>[</span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>grepl</a></span><span class='o'>(</span><span class='s'>"equals\\("</span>, <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>siblings</span><span class='o'>)</span><span class='o'>)</span><span class='o'>]</span></span>
<span>  <span class='kr'>if</span> <span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/length.html'>length</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span> <span class='o'>==</span> <span class='m'>0</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>    <span class='nf'>cli</span><span class='nf'>::</span><span class='nf'><a href='https://cli.r-lib.org/reference/cli_alert.html'>cli_alert_warning</a></span><span class='o'>(</span><span class='s'>"WARNING AT &#123;path&#125;."</span><span class='o'>)</span></span>
<span>    <span class='kr'><a href='https://rdrr.io/r/base/function.html'>return</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span>  <span class='o'>&#125;</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>xml</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='s'>"expect_equal"</span></span>
<span>  <span class='nv'>text</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_contents</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span><span class='o'>[[</span><span class='m'>3</span><span class='o'>]</span><span class='o'>]</span> <span class='o'>|&gt;</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_replace.html'>xml_remove</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_children.html'>xml_contents</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span><span class='o'>)</span></span>
<span>  <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>equal</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='nv'>text</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='nv'>paths</span> <span class='o'>&lt;-</span> <span class='nf'>fs</span><span class='nf'>::</span><span class='nf'><a href='https://fs.r-lib.org/reference/dir_ls.html'>dir_ls</a></span><span class='o'>(</span><span class='s'>"tests/testthat"</span>, regex <span class='o'>=</span> <span class='s'>"test-"</span><span class='o'>)</span></span>
<span></span>
<span><span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/map.html'>walk</a></span><span class='o'>(</span><span class='nv'>paths</span>, <span class='nv'>parse_script</span><span class='o'>)</span></span></code></pre>
</div>

[Example PR](https://github.com/igraph/rigraph/pull/1337)

## Conclusion

In this post I presented a strategy that served me well when refactoring igraph's test scripts: parsing code to XML, editing it as XML, then writing back the edited lines thanks to the attributes of XML nodes that indicate their original lines in the script.

Other possible approaches include styler's parsing of code into a table and [serialization](https://github.com/r-lib/styler) of that table.

In a more similar approach, which means it might have been wise for me to explore this codebase sooner :sweat_smile:, the codegrip package uses [xmlparsedata](https://github.com/lionel-/codegrip/blob/82d164ed91ad819587796a4053b1df5b0385c677/R/ast.R#L1) and has [helpers for finding which lines a node refers to](https://github.com/lionel-/codegrip/blob/82d164ed91ad819587796a4053b1df5b0385c677/R/ast.R#L51).

Do you sometimes use automatic refactoring ([styler](https://styler.r-lib.org/), [codegrip](https://github.com/lionel-/codegrip), etc.), or automatic diagnostics ([lintr](https://lintr.r-lib.org/), [pkgcheck](https://docs.ropensci.org/pkgcheck/), etc.)? Have you written any customization or standalone script to help you with that?

