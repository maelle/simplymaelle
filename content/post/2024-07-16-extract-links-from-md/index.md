---
title: "Extracting all links from my slidedeck"
date: '2024-07-16'
slug: extract-links-from-md
output: hugodown::hugo_document
tags:
  - xml
  - tinkr
rmd_hash: 95a1d52071cb55ca

---

Last week after my [useR! talk](/talks/2024-07-10-user-2024-rusty-code/), someone I had met at the R-Ladies dinner asked me for a list of all the links in my slides. I said I'd prepare it, not because I'm a nice person, but because I knew it'd be an use case where the great tinkr package would shine! :smiling_imp:

## What is tinkr?

[tinkr](https://docs.ropensci.org/tinkr/) is an R package I [created](https://ropensci.org/blog/2018/10/01/tinkr/), and that its current maintainer [Zhian Kamvar](https://zkamvar.netlify.app/) took much further that I'd ever would have. tinkr can transform Markdown into XML and back.

Under the hood, tinkr uses

-   [commonmark](http://docs.ropensci.org/commonmark/) for the Markdown-to-XML conversion. CommonMark, in the form of its cmark implementation, is the C library that GitHub for instance uses to display your Markdown comments as HTML. The commonmark package is also what powers [Markdown support in roxygen2](https://roxygen2.r-lib.org/articles/rd-formatting.html).
-   [xslt](https://docs.ropensci.org/xslt/) for the XML-to-Markdown conversion. XSLT is a templating language for XML.

Anyway, enough said, let's go back to today's use case.

## Extract and format links from `index.qmd`

With tinkr I can use XPath, the query language for XML or HTML, to extract links from my slidedeck source. Then I will format them as a list.

First, I create a yarn object from my slidedeck source.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>talk_yarn</span> <span class='o'>&lt;-</span> <span class='nf'>tinkr</span><span class='nf'>::</span><span class='nv'><a href='https://docs.ropensci.org/tinkr/reference/yarn.html'>yarn</a></span><span class='o'>$</span><span class='nf'>new</span><span class='o'>(</span><span class='s'>"/home/maelle/Documents/conferences/user2024/index.qmd"</span><span class='o'>)</span></span>
<span><span class='nv'>talk_yarn</span></span>
<span><span class='c'>#&gt; &lt;yarn&gt;</span></span>
<span><span class='c'>#&gt;   Public:</span></span>
<span><span class='c'>#&gt;     add_md: function (md, where = 0L) </span></span>
<span><span class='c'>#&gt;     body: xml_document, xml_node</span></span>
<span><span class='c'>#&gt;     clone: function (deep = FALSE) </span></span>
<span><span class='c'>#&gt;     get_protected: function (type = NULL) </span></span>
<span><span class='c'>#&gt;     head: function (n = 6L, stylesheet_path = stylesheet()) </span></span>
<span><span class='c'>#&gt;     initialize: function (path = NULL, encoding = "UTF-8", sourcepos = FALSE, </span></span>
<span><span class='c'>#&gt;     md_vec: function (xpath = NULL, stylesheet_path = stylesheet()) </span></span>
<span><span class='c'>#&gt;     ns: http://commonmark.org/xml/1.0</span></span>
<span><span class='c'>#&gt;     path: /home/maelle/Documents/conferences/user2024/index.qmd</span></span>
<span><span class='c'>#&gt;     protect_curly: function () </span></span>
<span><span class='c'>#&gt;     protect_math: function () </span></span>
<span><span class='c'>#&gt;     protect_unescaped: function () </span></span>
<span><span class='c'>#&gt;     reset: function () </span></span>
<span><span class='c'>#&gt;     show: function (lines = TRUE, stylesheet_path = stylesheet()) </span></span>
<span><span class='c'>#&gt;     tail: function (n = 6L, stylesheet_path = stylesheet()) </span></span>
<span><span class='c'>#&gt;     write: function (path = NULL, stylesheet_path = stylesheet()) </span></span>
<span><span class='c'>#&gt;     yaml: --- format:   revealjs:       highlight-style: a11y      ...</span></span>
<span><span class='c'>#&gt;   Private:</span></span>
<span><span class='c'>#&gt;     encoding: UTF-8</span></span>
<span><span class='c'>#&gt;     md_lines: function (path = NULL, stylesheet = NULL) </span></span>
<span><span class='c'>#&gt;     sourcepos: FALSE</span></span>
<span></span></code></pre>

</div>

Then I extract all links.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>links</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_all</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>talk_yarn</span><span class='o'>$</span><span class='nv'>body</span>, </span>
<span>  xpath <span class='o'>=</span> <span class='s'>".//md:link"</span>,</span>
<span>  ns <span class='o'>=</span> <span class='nv'>talk_yarn</span><span class='o'>$</span><span class='nv'>ns</span></span>
<span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>links</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; &#123;xml_nodeset (6)&#125;</span></span>
<span><span class='c'>#&gt; [1] &lt;link destination="https://user-maelle.netlify.app" title=""&gt;\n  &lt;text xm ...</span></span>
<span><span class='c'>#&gt; [2] &lt;link destination="https://www.pexels.com/photo/old-cargo-ship-on-sea-207 ...</span></span>
<span><span class='c'>#&gt; [3] &lt;link destination="https://www.pexels.com/photo/the-word-louise-is-spelle ...</span></span>
<span><span class='c'>#&gt; [4] &lt;link destination="https://www.pexels.com/photo/gray-rotary-telephone-on- ...</span></span>
<span><span class='c'>#&gt; [5] &lt;link destination="https://www.pexels.com/photo/close-up-photography-of-y ...</span></span>
<span><span class='c'>#&gt; [6] &lt;link destination="https://www.r-consortium.org/all-projects/call-for-pro ...</span></span>
<span></span></code></pre>

</div>

I then throw away the links to the great website Pexels, because these are image credits rather than information useful to do R stuff.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>links</span> <span class='o'>&lt;-</span> <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/keep.html'>discard</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>links</span>, </span>
<span>  \<span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='nf'><a href='https://rdrr.io/r/base/startsWith.html'>startsWith</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>x</span>, <span class='s'>"destination"</span><span class='o'>)</span>, <span class='s'>"https://www.pexels"</span><span class='o'>)</span></span>
<span><span class='o'>)</span></span>
<span><span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>links</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; &#123;xml_nodeset (6)&#125;</span></span>
<span><span class='c'>#&gt; [1] &lt;link destination="https://user-maelle.netlify.app" title=""&gt;\n  &lt;text xm ...</span></span>
<span><span class='c'>#&gt; [2] &lt;link destination="https://www.r-consortium.org/all-projects/call-for-pro ...</span></span>
<span><span class='c'>#&gt; [3] &lt;link destination="https://www.r-consortium.org/all-projects/call-for-pro ...</span></span>
<span><span class='c'>#&gt; [4] &lt;link destination="https://www.heltweg.org/posts/who-wrote-this-shit/" ti ...</span></span>
<span><span class='c'>#&gt; [5] &lt;link destination="https://fosstodon.org/@hadleywickham/11202130903588421 ...</span></span>
<span><span class='c'>#&gt; [6] &lt;link destination="https://nostarch.com/kill-it-fire" title=""&gt;\n  &lt;text  ...</span></span>
<span></span></code></pre>

</div>

After that I can format the links and display them here using an ["asis" chunk](https://bookdown.org/yihui/rmarkdown-cookbook/results-asis.html). Yes, my slidedeck uses Quarto but this blog is still powered by R Markdown, [hugodown](https://hugodown.r-lib.org/) to be precise.

I'm using the formatting as an opportunity to only keep distinct links: sometimes I had very similar slides in a row, with repeated information.

<div class='highlight'>
<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>format_link</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>link</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nv'>url</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_attr.html'>xml_attr</a></span><span class='o'>(</span><span class='nv'>link</span>, <span class='s'>"destination"</span><span class='o'>)</span></span>
<span>  <span class='nv'>text</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>link</span><span class='o'>)</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/sprintf.html'>sprintf</a></span><span class='o'>(</span><span class='s'>"* [%s](%s)"</span>, <span class='nv'>text</span>, <span class='nv'>url</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span>
<span></span>
<span><span class='nv'>formatted_links</span> <span class='o'>&lt;-</span> <span class='nf'>purrr</span><span class='nf'>::</span><span class='nf'><a href='https://purrr.tidyverse.org/reference/map.html'>map_chr</a></span><span class='o'>(</span><span class='nv'>links</span>, <span class='nv'>format_link</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>formatted_links</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/unique.html'>unique</a></span><span class='o'>(</span><span class='nv'>formatted_links</span><span class='o'>)</span></span>
<span></span>
<span><span class='nv'>formatted_links</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/paste.html'>paste</a></span><span class='o'>(</span>collapse <span class='o'>=</span> <span class='s'>"\n"</span><span class='o'>)</span> <span class='o'>|&gt;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/cat.html'>cat</a></span><span class='o'>(</span><span class='o'>)</span></span>
</code></pre>

-   <https://user-maelle.netlify.app>
-   [R Consortium ISC](https://www.r-consortium.org/all-projects/call-for-proposals)
-   <https://www.heltweg.org/posts/who-wrote-this-shit/>
-   [https://fosstodon.org/@hadleywickham/112021309035884210](https://fosstodon.org/@hadleywickham/112021309035884210)
-   <https://nostarch.com/kill-it-fire>
-   ["Refactoring Pro-Tip: Easiest Nearest Owwie First"](https://www.geepawhill.org/2019/03/03/refactoring-pro-tip-easiest-nearest-owwie-first/)
-   <https://styler.r-lib.org/>
-   <https://masalmon.eu/2024/05/23/refactoring-tests/>
-   [{lintr} itself](https://lintr.r-lib.org/)
-   [reference index](https://lintr.r-lib.org/reference/index.html)
-   [continuous integration](https://github.com/r-lib/actions/tree/v2-branch/examples)
-   <https://masalmon.eu/2024/05/15/refactoring-xml/>
-   [Tidyteam code review principles](https://code-review.tidyverse.org/)
-   [The Code Review Anxiety Workbook](https://developer-success-lab.gitbook.io/code-review-anxiety-workbook-1)
-   [General science lifecycle](https://ropensci.org/software-review/)
-   [Statistical software](https://ropensci.org/stat-software-review/)
-   [now](https://github.com/rstats-wtf/wtf-version-control-slides)
-   [then](https://github.com/rstudio-conf-2022/wtf-rstats/tree/main/materials)
-   [Happy Git and GitHub for the useR](https://happygitwithr.com/)
-   ["Oh shit, Git!"](https://wizardzines.com/zines/oh-shit-git/)
-   ["How Git works"](https://wizardzines.com/zines/git/)
-   [Why you need small, informative Git commits](https://masalmon.eu/2024/06/03/small-commits/)
-   [The two phases of commits in a Git branch](https://masalmon.eu/2023/12/07/two-phases-git-branches/)
-   [Hack your way to a good Git history](https://masalmon.eu/2024/06/11/rewrite-git-history/)
-   [{saperlipopette}](https://docs.ropensci.org/saperlipopette/)
-   [Oh shit, Git!](https://ohshitgit.com/)
-   [No Maintenance Intended](http://unmaintained.tech/)
-   [What Does It Mean to Maintain a Package?](https://ropensci.org/blog/2023/02/07/what-does-it-mean-to-maintain-a-package/)
-   [Three currencies of payment for our work](https://yabellini.netlify.app/blog/2023-10-13-three-payments/)
-   [Package maintainer cheatsheet](https://devguide.ropensci.org/maintenance_cheatsheet.html)
-   [dev guide](https://devguide.ropensci.org/)
-   [blog](https://ropensci.org/blog)
-   [Package Development Corner](https://ropensci.org/news)
-   [paths of participation](https://contributing.ropensci.org/)
-   [Monthly newsletter](https://ropensci.org/news)
-   [Blog](https://ropensci.org/blog)
-   [R-universe](https://docs.r-universe.dev/)
-   <https://ropensci.org/help-wanted>
-   <https://ropensci.org/news>
-   <https://devguide.ropensci.org/maintenance_evolution.html#archivalguidance>
-   [2021 community call](https://ropensci.org/commcalls/apr2021-pkg-community/)
    </div>

## Conclusion

Using tinkr, XPath and [`sprintf()`](/2023/09/29/three-functions/), I was able to create a list of all the links shared in my useR! slidedeck. Some of them have no text, meaning that the URL is used as text for the link; or text that only makes sense in the context of the paragraph they were a part of; others are slightly more informative; but at least none of them is a ["click here" link](https://dap.berkeley.edu/learn/concepts/links). :sweat_smile:

