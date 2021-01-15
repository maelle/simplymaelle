---
title: "Stingy Beanie baby webscraping"
date: '2021-01-15'
tags:
  - polite
slug: beanie-baby
output: hugodown::hugo_document
rmd_hash: d44f0470d475ed7a

---

I've just finished teaching blogging with R Markdown at R-Ladies Bangalore. This has two consequences: I need to calm down a bit, and I am inspired to, well, blog with R Markdown! As I've just read a [fascinating book about the beanie baby fad](https://www.goodreads.com/book/show/20821185-the-great-beanie-baby-bubble) and as I've seen rvest is getting an update, I've decided to harvest [Beaniepedia](https://beaniepedia.com). Both of these things show I spend too much time on Twitter, as the book has been tweeted about [by Vicky Boykis](https://twitter.com/vboykis/status/1347575684802240512), and the package changes have been tweeted about [by Hadley Wickham](https://twitter.com/hadleywickham/status/1347260116932976643). I call that [staying up-to-date](/2019/01/25/uptodate/), of course.

So, as a little challenge for today, what are the most common animals among Beanie babies? Do I even need much webscraping to find this out?

How does one webscrape these days
---------------------------------

I've always liked webscraping, as I think I enjoy getting and transforming data more than I enjoy analyzing it. Compared to my former self,

-   I know a bit more about XPath (starting with knowing it exists!) so I don't use regular expression to parse HTML.
-   I use [polite](https://dmi3kno.github.io/polite/) for polite webscraping!
-   I know it's best to spend more time pondering about strategies before hammering requests at a website.

As to rvest recent changes, I had a quick look at the [changelog](https://rvest.tidyverse.org/news/index.html) but since I hadn't used it in so long, it's not as if I had any habit to change!

How to harvest Beaniepedia
--------------------------

I've noticed Beaniepedia has a [sitemap for all beanies](view-source:https://beaniepedia.com/beanies/sitemap.xml) so from that I can extract the URLs to all Beanie pages. That's a necessary step.

Now from there I could either

-   Scrape each of this page, respectfully slowly, and extract the table that includes the beanie's information;
-   Use a more frugal strategy by parsing URLs. E.g. from the path of `https://beaniepedia.com/beanies/beanie-babies/january-the-birthday-bear-series-2/` I can extract the category of the Beanie (a beanie baby as opposed to, say, an attic treasure) and the animal by splitting `january-the-birthday-bear-series-2` into pieces and see whether one is an animal. How would I recognize animals? By using an rcorpora dataset.

I'll choose the second strategy and leave the first one as an exercise to the reader. :wink:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='c'># I found the right argument via rcorpora::categories() </span>
<span class='c'># and rcorpora::corpora(category = "animals")</span>
<span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'>rcorpora</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/rcorpora/man/corpora.html'>corpora</a></span><span class='o'>(</span><span class='s'>"animals/common"</span><span class='o'>)</span>
<span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>animals</span><span class='o'>$</span><span class='nv'>animals</span><span class='o'>)</span>

<span class='c'>#&gt; [1] "aardvark"  "alligator" "alpaca"    "antelope"  "ape"       "armadillo"</span>
</code></pre>

</div>

Let's get to work! A *sine qua non* condition is obviously the website being ok with our scraping stuff. The polite package would tell us whether the robots.txt file were against our doing this, and I also took time looking whether the website had any warning. I didn't find any so I think we're good to go.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>session</span> <span class='o'>&lt;-</span> <span class='nf'>polite</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/polite/man/bow.html'>bow</a></span><span class='o'>(</span>
  url <span class='o'>=</span> <span class='s'>"https://beaniepedia.com/beanies/sitemap.xml"</span>,
  user_agent <span class='o'>=</span> <span class='s'>"Maëlle Salmon https://masalmon.eu"</span>
  <span class='o'>)</span>
<span class='nv'>sitemap</span> <span class='o'>&lt;-</span> <span class='nf'>polite</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/polite/man/scrape.html'>scrape</a></span><span class='o'>(</span><span class='nv'>session</span><span class='o'>)</span>
<span class='nv'>sitemap</span>

<span class='c'>#&gt; &#123;xml_document&#125;</span>
<span class='c'>#&gt; &lt;urlset schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://www.sitemaps.org/schemas/sitemap/0.9"&gt;</span>
<span class='c'>#&gt;  [1] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/&lt;/loc&gt;\n  &lt;lastmod&gt;2021-01 ...</span>
<span class='c'>#&gt;  [2] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jerry-the-mi ...</span>
<span class='c'>#&gt;  [3] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/snowball-the ...</span>
<span class='c'>#&gt;  [4] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jemima-puddl ...</span>
<span class='c'>#&gt;  [5] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jeff-gordon- ...</span>
<span class='c'>#&gt;  [6] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jeff-burton- ...</span>
<span class='c'>#&gt;  [7] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jeepers-the- ...</span>
<span class='c'>#&gt;  [8] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jeanette-the ...</span>
<span class='c'>#&gt;  [9] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/jaz-the-cat/ ...</span>
<span class='c'>#&gt; [10] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/japan-the-be ...</span>
<span class='c'>#&gt; [11] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/laughter-the ...</span>
<span class='c'>#&gt; [12] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/righty-2000- ...</span>
<span class='c'>#&gt; [13] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/lefty-2004-t ...</span>
<span class='c'>#&gt; [14] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/lefty-the-do ...</span>
<span class='c'>#&gt; [15] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/lefty-the-do ...</span>
<span class='c'>#&gt; [16] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/january-the- ...</span>
<span class='c'>#&gt; [17] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/january-the- ...</span>
<span class='c'>#&gt; [18] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/beanie-babies/janglemouse- ...</span>
<span class='c'>#&gt; [19] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/attic-treasures/burrows-th ...</span>
<span class='c'>#&gt; [20] &lt;url&gt;\n  &lt;loc&gt;https://beaniepedia.com/beanies/attic-treasures/klause-the ...</span>
<span class='c'>#&gt; ...</span>
</code></pre>

</div>

The `sitemap` object is an XML document. I will extract URLs with the xml2 package.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>sitemap</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_ns_strip.html'>xml_ns_strip</a></span><span class='o'>(</span><span class='nv'>sitemap</span><span class='o'>)</span>
<span class='nv'>urls</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_all</a></span><span class='o'>(</span><span class='nv'>sitemap</span>, <span class='s'>".//loc"</span><span class='o'>)</span><span class='o'>)</span>
<span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>urls</span><span class='o'>)</span>

<span class='c'>#&gt; [1] "https://beaniepedia.com/beanies/"                                          </span>
<span class='c'>#&gt; [2] "https://beaniepedia.com/beanies/beanie-babies/jerry-the-minion-2/"         </span>
<span class='c'>#&gt; [3] "https://beaniepedia.com/beanies/beanie-babies/snowball-the-snowman/"       </span>
<span class='c'>#&gt; [4] "https://beaniepedia.com/beanies/beanie-babies/jemima-puddle-duck-the-duck/"</span>
<span class='c'>#&gt; [5] "https://beaniepedia.com/beanies/beanie-babies/jeff-gordon-24-the-bear/"    </span>
<span class='c'>#&gt; [6] "https://beaniepedia.com/beanies/beanie-babies/jeff-burton-no-31-the-bear/"</span>
</code></pre>

</div>

Now I need to parse the URLs. In an URL path like `beanies/beanie-babies/jerry-the-minion-2/` the second part is the category, the third part is the Beanie Baby name. I, as if I were a good collector :upside_down_face:, am not interested in Attic treasures, only in Beanie Babies.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>urls_df</span> <span class='o'>&lt;-</span> <span class='nf'>urltools</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/urltools/man/url_parse.html'>url_parse</a></span><span class='o'>(</span><span class='nv'>urls</span><span class='o'>)</span>
<span class='nv'>urls_df</span> <span class='o'>&lt;-</span> <span class='nf'>dplyr</span><span class='nf'>::</span><span class='nf'><a href='https://dplyr.tidyverse.org/reference/filter.html'>filter</a></span><span class='o'>(</span><span class='nv'>urls_df</span>, <span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_detect.html'>str_detect</a></span><span class='o'>(</span><span class='nv'>path</span>, <span class='s'>"beanie-babies"</span><span class='o'>)</span><span class='o'>)</span>
</code></pre>

</div>

This gives me 632 Beanie babies. Let's parse the last part of their path.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='s'><a href='https://magrittr.tidyverse.org'>"magrittr"</a></span><span class='o'>)</span>
<span class='nv'>urls_df</span> <span class='o'>&lt;-</span> <span class='nv'>urls_df</span> <span class='o'>%&gt;%</span>
  <span class='nf'>dplyr</span><span class='nf'>::</span><span class='nf'><a href='https://dplyr.tidyverse.org/reference/rowwise.html'>rowwise</a></span><span class='o'>(</span><span class='o'>)</span> <span class='o'>%&gt;%</span>
  <span class='nf'>dplyr</span><span class='nf'>::</span><span class='nf'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='o'>(</span>parsed_path <span class='o'>=</span> <span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_split.html'>str_split</a></span><span class='o'>(</span><span class='nv'>path</span>, <span class='s'>"/"</span>, simplify <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span><span class='o'>[</span><span class='m'>1</span>,<span class='m'>3</span><span class='o'>]</span><span class='o'>)</span> <span class='o'>%&gt;%</span>
  <span class='nf'>dplyr</span><span class='nf'>::</span><span class='nf'><a href='https://dplyr.tidyverse.org/reference/mutate.html'>mutate</a></span><span class='o'>(</span>parsed_path <span class='o'>=</span> <span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_split.html'>str_split</a></span><span class='o'>(</span><span class='nv'>parsed_path</span>, <span class='s'>"-"</span><span class='o'>)</span><span class='o'>)</span>
<span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>urls_df</span><span class='o'>$</span><span class='nv'>parsed_path</span><span class='o'>)</span>

<span class='c'>#&gt; [[1]]</span>
<span class='c'>#&gt; [1] "jerry"  "the"    "minion" "2"     </span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; [[2]]</span>
<span class='c'>#&gt; [1] "snowball" "the"      "snowman" </span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; [[3]]</span>
<span class='c'>#&gt; [1] "jemima" "puddle" "duck"   "the"    "duck"  </span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; [[4]]</span>
<span class='c'>#&gt; [1] "jeff"   "gordon" "24"     "the"    "bear"  </span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; [[5]]</span>
<span class='c'>#&gt; [1] "jeff"   "burton" "no"     "31"     "the"    "bear"  </span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; [[6]]</span>
<span class='c'>#&gt; [1] "jeepers" "the"     "tiger"</span>
</code></pre>

</div>

Will "jemima-puddle-duck-the-duck" be a problem? Hopefully not. Now for each parsed path let's see whether it corresponds to any common animal.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'>

<span class='nv'>animals</span> <span class='o'>&lt;-</span> <span class='nf'>tibble</span><span class='nf'>::</span><span class='nf'><a href='https://tibble.tidyverse.org/reference/tibble.html'>tibble</a></span><span class='o'>(</span>animal <span class='o'>=</span> <span class='nv'>animals</span><span class='o'>)</span>
</code></pre>

</div>

