---
title: "Server-side MathJax rendering with R?"
date: '2021-06-15'
tags:
  - chromote
slug: serverside-mathjax
output: hugodown::hugo_document
rmd_hash: 4556a3a8a12f15b9

---

Whilst I most certainly do not write LaTeX formulas on the regular anymore, I got curious about their [MathJax](https://www.mathjax.org/) rendering on websites.[^1] In brief : your website source contains LaTeX code, and the MathJax JS library (self-hosted or hosted on a CDN) transforms it into something humans can understand: some HTML with inline CSS but also some MathML for screen-reader users.[^2] As I quite like the idea of moving things from the client-side to the server-side, I started wondering whether the processing of LaTeX code could happen before an user opens a website. Searching for "server-side MathJax rendering" on the web gave a few hits. A few hits only, sure, which shows how niche the topic is, and meant reading resources was not too verwhelming. :grin: In this post I am reporting on my findings.

## Why render MathJax on the server-side

Let me be honest, there is no extremely strong reason for *me*. Sure loading MathJax from a CDN might be a privacy problem (as the CDN measures traffic) or make your website potentially fragile if the CDN goes down (remember the [Fastly outage](https://en.wikipedia.org/wiki/Fastly#Operation) last week). Now you could still self-host MathJax to mitigate that (and to know exactly who to blame in case of an outage i.e. yourself :wink:). Not having MathJax might mean the page takes less time to load... if having the MathJax-rendered HTML weren't making the page bigger than the same page with LaTeX code instead. But hey I still think it's an interesting problem.

Something I realized whilst working on this post is that one probably does not want to remove all MathJax JS from the browser, as it includes [accessibility options](https://docs.mathjax.org/en/v2.7-latest/options/extensions/a11y-extensions.html#a11y-extensions)! You need some JS in order to have a menu etc. That's not something I've tackled here, I think that to extract the right MathJax JS to have only the components for the menu but not those for processign input, one would need to understand MathJax much better than I do.

## How to render MathJax on the server-side

The recipe would be:

-   extract LaTeX code. E.g. use XPath with xml2 for HTML; or regular expressions if that's your jam.
-   transform the LaTeX string into HTML somehow.
-   replace the LaTeX code with the HTML.

The first and second steps are things close to e.g. the [HTML tweaking pkgdown does](https://github.com/r-lib/pkgdown/blob/master/R/html-tweak.R). They might well shall entail (un)escaping problems.

Now the second step, how to transform LaTeX code into HTML? The best summary of the state-of-the-art I found is the blog post [Math Rendering is Wrong](https://danilafe.com/blog/math_rendering_is_wrong/).

Solutions I saw are:

-   a Node module called [mathjax-node-cli](https://github.com/mathjax/mathjax-node-cli). I know there are [ways to use JS from R](https://blog.r-hub.io/2020/08/25/js-r/#bundling-javascript-code) but I got lazy once I saw I had to update my Node installation or whatever the error message was.
-   a way to run a [MathJax API](https://github.com/chialab/math-api), which I have not tried.
-   rendering math [on a browser and extracting the result](https://advancedweb.hu/mathjax-processing-on-the-server-side/).

I will now focus on the last solution as it works well from R these days.

## MathJax, but not in the viewer's browser!

Let's take a minimal HTML with MathJax loaded from a CDN and an empty paragraph with class "mathp":

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>html</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_html</a></span><span class='o'>(</span><span class='s'>'&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;title&gt;MathJax&lt;/title&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;meta name="viewport" content="width=device-width, initial-scale=1"&gt;
&lt;script type="text/javascript" src="https://mathjax.rstudio.com/2.7.2/MathJax.js?config=TeX-AMS-MML_HTMLorMML"&gt;&lt;/script&gt;

&lt;/head&gt;
&lt;body&gt;

&lt;p class="mathp"&gt;

&lt;/p&gt;

&lt;/body&gt;
&lt;/html&gt;
'</span><span class='o'>)</span></code></pre>

</div>

Now let's add math to it. Note that a real solution would have to differentiate in-line and block maths whose resulting HTML has a different XPath.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>mathp</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_first</a></span><span class='o'>(</span><span class='nv'>html</span>, <span class='s'>"//p[@class='mathp']"</span><span class='o'>)</span>
<span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>mathp</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='s'>r"($$x = &#123;-b \pm \sqrt&#123;b^2-4ac&#125; \over 2a&#125;.$$)"</span>
<span class='nf'><a href='https://rdrr.io/r/base/character.html'>as.character</a></span><span class='o'>(</span><span class='nv'>html</span><span class='o'>)</span>
<span class='c'>#&gt; [1] "&lt;!DOCTYPE html&gt;\n&lt;html&gt;\n&lt;head&gt;\n&lt;title&gt;MathJax&lt;/title&gt;\n&lt;meta http-equiv=\"Content-Type\" content=\"text/html; charset=UTF-8\"&gt;\n&lt;meta name=\"viewport\" content=\"width=device-width, initial-scale=1\"&gt;\n&lt;script type=\"text/javascript\" src=\"https://mathjax.rstudio.com/2.7.2/MathJax.js?config=TeX-AMS-MML_HTMLorMML\"&gt;&lt;/script&gt;\n&lt;/head&gt;\n&lt;body&gt;\n\n&lt;p class=\"mathp\"&gt;$$x = &#123;-b \\pm \\sqrt&#123;b^2-4ac&#125; \\over 2a&#125;.$$&lt;/p&gt;\n\n&lt;/body&gt;\n&lt;/html&gt;\n"</span>
<span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/write_xml.html'>write_html</a></span><span class='o'>(</span><span class='nv'>html</span>, <span class='s'>"raw.html"</span><span class='o'>)</span></code></pre>

</div>

Now we shall load the file in a browser via [chromote](https://github.com/rstudio/chromote/)[^3] and extract the HTML Pandoc produced. To find the XPath, I examined the HTML with the DevTools of my browser.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='kr'><a href='https://rdrr.io/r/base/library.html'>library</a></span><span class='o'>(</span><span class='s'>"chromote"</span><span class='o'>)</span>
<span class='nv'>b</span> <span class='o'>&lt;-</span> <span class='nv'><a href='https://rdrr.io/pkg/chromote/man/ChromoteSession.html'>ChromoteSession</a></span><span class='o'>$</span><span class='nf'>new</span><span class='o'>(</span><span class='o'>)</span>
<span class='nv'>b</span><span class='o'>$</span><span class='nv'>Page</span><span class='o'>$</span><span class='nf'>navigate</span><span class='o'>(</span><span class='s'>"https://masalmon.eu/2021/06/15/serverside-mathjax/raw.html"</span><span class='o'>)</span> 
<span class='c'>#&gt; $frameId</span>
<span class='c'>#&gt; [1] "84F1B0DAF37C9D58D842202AD3552FF4"</span>
<span class='c'>#&gt; </span>
<span class='c'>#&gt; $loaderId</span>
<span class='c'>#&gt; [1] "5705A83187AB50084AE28A9D22D15CAB"</span>
<span class='c'># Make sure we wait long enough</span>
<span class='nf'><a href='https://rdrr.io/r/base/Sys.sleep.html'>Sys.sleep</a></span><span class='o'>(</span><span class='m'>2</span><span class='o'>)</span>
<span class='nv'>doc</span> <span class='o'>&lt;-</span> <span class='nv'>b</span><span class='o'>$</span><span class='nv'>DOM</span><span class='o'>$</span><span class='nf'>getDocument</span><span class='o'>(</span><span class='o'>)</span>
<span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nv'>b</span><span class='o'>$</span><span class='nv'>DOM</span><span class='o'>$</span><span class='nf'>querySelector</span><span class='o'>(</span><span class='nv'>doc</span><span class='o'>$</span><span class='nv'>root</span><span class='o'>$</span><span class='nv'>nodeId</span>, <span class='s'>".MathJax_Display"</span><span class='o'>)</span>
<span class='o'>(</span><span class='nv'>math_html</span> <span class='o'>&lt;-</span> <span class='nv'>b</span><span class='o'>$</span><span class='nv'>DOM</span><span class='o'>$</span><span class='nf'>getOuterHTML</span><span class='o'>(</span><span class='nv'>x</span><span class='o'>$</span><span class='nv'>nodeId</span><span class='o'>)</span><span class='o'>)</span>
<span class='c'>#&gt; $outerHTML</span>
<span class='c'>#&gt; [1] "&lt;div class=\"MathJax_Display\" style=\"text-align: center;\"&gt;&lt;span class=\"MathJax\" id=\"MathJax-Element-1-Frame\" tabindex=\"0\" data-mathml=\"&lt;math xmlns=&amp;quot;http://www.w3.org/1998/Math/MathML&amp;quot; display=&amp;quot;block&amp;quot;&gt;&lt;mi&gt;x&lt;/mi&gt;&lt;mo&gt;=&lt;/mo&gt;&lt;mrow class=&amp;quot;MJX-TeXAtom-ORD&amp;quot;&gt;&lt;mfrac&gt;&lt;mrow&gt;&lt;mo&gt;&amp;amp;#x2212;&lt;/mo&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mo&gt;&amp;amp;#x00B1;&lt;/mo&gt;&lt;msqrt&gt;&lt;msup&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mn&gt;2&lt;/mn&gt;&lt;/msup&gt;&lt;mo&gt;&amp;amp;#x2212;&lt;/mo&gt;&lt;mn&gt;4&lt;/mn&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;mi&gt;c&lt;/mi&gt;&lt;/msqrt&gt;&lt;/mrow&gt;&lt;mrow&gt;&lt;mn&gt;2&lt;/mn&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;/mrow&gt;&lt;/mfrac&gt;&lt;/mrow&gt;&lt;mo&gt;.&lt;/mo&gt;&lt;/math&gt;\" role=\"presentation\" style=\"text-align: center; position: relative;\"&gt;&lt;nobr aria-hidden=\"true\"&gt;&lt;span class=\"math\" id=\"MathJax-Span-1\" style=\"width: 10.144em; display: inline-block;\"&gt;&lt;span style=\"display: inline-block; position: relative; width: 9.552em; height: 0px; font-size: 106%;\"&gt;&lt;span style=\"position: absolute; clip: rect(0.307em, 1009.47em, 3.054em, -1000em); top: -2.182em; left: 0em;\"&gt;&lt;span class=\"mrow\" id=\"MathJax-Span-2\"&gt;&lt;span class=\"mi\" id=\"MathJax-Span-3\" style=\"font-family: MathJax_Math-italic;\"&gt;x&lt;/span&gt;&lt;span class=\"mo\" id=\"MathJax-Span-4\" style=\"font-family: MathJax_Main; padding-left: 0.278em;\"&gt;=&lt;/span&gt;&lt;span class=\"texatom\" id=\"MathJax-Span-5\" style=\"padding-left: 0.278em;\"&gt;&lt;span class=\"mrow\" id=\"MathJax-Span-6\"&gt;&lt;span class=\"mfrac\" id=\"MathJax-Span-7\"&gt;&lt;span style=\"display: inline-block; position: relative; width: 7.116em; height: 0px; margin-right: 0.12em; margin-left: 0.12em;\"&gt;&lt;span style=\"position: absolute; clip: rect(2.879em, 1007em, 4.443em, -1000em); top: -4.753em; left: 50%; margin-left: -3.498em;\"&gt;&lt;span class=\"mrow\" id=\"MathJax-Span-8\"&gt;&lt;span class=\"mo\" id=\"MathJax-Span-9\" style=\"font-family: MathJax_Main;\"&gt;−&lt;/span&gt;&lt;span class=\"mi\" id=\"MathJax-Span-10\" style=\"font-family: MathJax_Math-italic;\"&gt;b&lt;/span&gt;&lt;span class=\"mo\" id=\"MathJax-Span-11\" style=\"font-family: MathJax_Main; padding-left: 0.222em;\"&gt;±&lt;/span&gt;&lt;span class=\"msqrt\" id=\"MathJax-Span-12\" style=\"padding-left: 0.222em;\"&gt;&lt;span style=\"display: inline-block; position: relative; width: 4.567em; height: 0px;\"&gt;&lt;span style=\"position: absolute; clip: rect(3.073em, 1003.54em, 4.268em, -1000em); top: -4.009em; left: 1em;\"&gt;&lt;span class=\"mrow\" id=\"MathJax-Span-13\"&gt;&lt;span class=\"msubsup\" id=\"MathJax-Span-14\"&gt;&lt;span style=\"display: inline-block; position: relative; width: 0.858em; height: 0px;\"&gt;&lt;span style=\"position: absolute; clip: rect(3.139em, 1000.42em, 4.197em, -1000em); top: -4.009em; left: 0em;\"&gt;&lt;span class=\"mi\" id=\"MathJax-Span-15\" style=\"font-family: MathJax_Math-italic;\"&gt;b&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"position: absolute; top: -4.298em; left: 0.429em;\"&gt;&lt;span class=\"mn\" id=\"MathJax-Span-16\" style=\"font-size: 70.7%; font-family: MathJax_Main;\"&gt;2&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;span class=\"mo\" id=\"MathJax-Span-17\" style=\"font-family: MathJax_Main; padding-left: 0.222em;\"&gt;−&lt;/span&gt;&lt;span class=\"mn\" id=\"MathJax-Span-18\" style=\"font-family: MathJax_Main; padding-left: 0.222em;\"&gt;4&lt;/span&gt;&lt;span class=\"mi\" id=\"MathJax-Span-19\" style=\"font-family: MathJax_Math-italic;\"&gt;a&lt;/span&gt;&lt;span class=\"mi\" id=\"MathJax-Span-20\" style=\"font-family: MathJax_Math-italic;\"&gt;c&lt;/span&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"position: absolute; clip: rect(3.56em, 1003.57em, 3.958em, -1000em); top: -4.69em; left: 1em;\"&gt;&lt;span style=\"display: inline-block; position: relative; width: 3.567em; height: 0px;\"&gt;&lt;span style=\"position: absolute; font-family: MathJax_Main; top: -4.009em; left: -0.084em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"position: absolute; font-family: MathJax_Main; top: -4.009em; left: 2.873em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"font-family: MathJax_Main; position: absolute; top: -4.009em; left: 0.388em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"font-family: MathJax_Main; position: absolute; top: -4.009em; left: 0.885em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"font-family: MathJax_Main; position: absolute; top: -4.009em; left: 1.382em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"font-family: MathJax_Main; position: absolute; top: -4.009em; left: 1.879em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"font-family: MathJax_Main; position: absolute; top: -4.009em; left: 2.376em;\"&gt;−&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"position: absolute; clip: rect(2.983em, 1001.02em, 4.536em, -1000em); top: -4.103em; left: 0em;\"&gt;&lt;span style=\"font-family: MathJax_Size1;\"&gt;√&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"position: absolute; clip: rect(3.167em, 1001.01em, 4.196em, -1000em); top: -3.323em; left: 50%; margin-left: -0.514em;\"&gt;&lt;span class=\"mrow\" id=\"MathJax-Span-21\"&gt;&lt;span class=\"mn\" id=\"MathJax-Span-22\" style=\"font-family: MathJax_Main;\"&gt;2&lt;/span&gt;&lt;span class=\"mi\" id=\"MathJax-Span-23\" style=\"font-family: MathJax_Math-italic;\"&gt;a&lt;/span&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 4.009em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"position: absolute; clip: rect(0.811em, 1007.12em, 1.238em, -1000em); top: -1.281em; left: 0em;\"&gt;&lt;span style=\"display: inline-block; overflow: hidden; vertical-align: 0em; border-top: 1.3px solid; width: 7.116em; height: 0px;\"&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 1.061em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;span class=\"mo\" id=\"MathJax-Span-24\" style=\"font-family: MathJax_Main;\"&gt;.&lt;/span&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; width: 0px; height: 2.182em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;/span&gt;&lt;span style=\"display: inline-block; overflow: hidden; vertical-align: -0.8em; border-left: 0px solid; width: 0px; height: 2.662em;\"&gt;&lt;/span&gt;&lt;/span&gt;&lt;/nobr&gt;&lt;span class=\"MJX_Assistive_MathML MJX_Assistive_MathML_Block\" role=\"presentation\"&gt;&lt;math xmlns=\"http://www.w3.org/1998/Math/MathML\" display=\"block\"&gt;&lt;mi&gt;x&lt;/mi&gt;&lt;mo&gt;=&lt;/mo&gt;&lt;mrow class=\"MJX-TeXAtom-ORD\"&gt;&lt;mfrac&gt;&lt;mrow&gt;&lt;mo&gt;−&lt;/mo&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mo&gt;±&lt;/mo&gt;&lt;msqrt&gt;&lt;msup&gt;&lt;mi&gt;b&lt;/mi&gt;&lt;mn&gt;2&lt;/mn&gt;&lt;/msup&gt;&lt;mo&gt;−&lt;/mo&gt;&lt;mn&gt;4&lt;/mn&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;mi&gt;c&lt;/mi&gt;&lt;/msqrt&gt;&lt;/mrow&gt;&lt;mrow&gt;&lt;mn&gt;2&lt;/mn&gt;&lt;mi&gt;a&lt;/mi&gt;&lt;/mrow&gt;&lt;/mfrac&gt;&lt;/mrow&gt;&lt;mo&gt;.&lt;/mo&gt;&lt;/math&gt;&lt;/span&gt;&lt;/span&gt;&lt;/div&gt;"</span></code></pre>

</div>

The `math_html` above is the MathJax-rendered math HTML we were after!

Now we create a minimal HTML without MathJax and add the math HTML to it.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='nv'>html</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_html</a></span><span class='o'>(</span><span class='s'>'&lt;!DOCTYPE html&gt;
&lt;html&gt;
&lt;head&gt;
&lt;title&gt;MathJax&lt;/title&gt;
&lt;meta http-equiv="Content-Type" content="text/html; charset=UTF-8" /&gt;
&lt;meta name="viewport" content="width=device-width, initial-scale=1"&gt;
&lt;/head&gt;
&lt;body&gt;

&lt;p id="mathp" class="mathp"&gt;

&lt;/p&gt;

&lt;/body&gt;
&lt;/html&gt;
'</span><span class='o'>)</span>
<span class='nv'>mathp</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_find_all.html'>xml_find_first</a></span><span class='o'>(</span><span class='nv'>html</span>, <span class='s'>"//p[@id='mathp']"</span><span class='o'>)</span>
<span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/xml_text.html'>xml_text</a></span><span class='o'>(</span><span class='nv'>mathp</span><span class='o'>)</span> <span class='o'>&lt;-</span> <span class='nv'>math_html</span><span class='o'>$</span><span class='nv'>outerHTML</span></code></pre>

</div>

Sadly the step above means the math HTML will have been escaped. One could find an actual fix, but my fix will be to unescape the HTML à la pkgdown i.e. literally using an internal function of pkgdown's.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span class='c'># from https://github.com/r-lib/pkgdown/blob/23eb05ceceda1c44573b254dd8b96e92cd91f825/R/html-build.R#L49</span>
<span class='nv'>unescape_html</span> <span class='o'>&lt;-</span> <span class='kr'>function</span><span class='o'>(</span><span class='nv'>x</span><span class='o'>)</span> <span class='o'>&#123;</span>
  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/grep.html'>gsub</a></span><span class='o'>(</span><span class='s'>"&amp;lt;"</span>, <span class='s'>"&lt;"</span>, <span class='nv'>x</span><span class='o'>)</span>
  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/grep.html'>gsub</a></span><span class='o'>(</span><span class='s'>"&amp;gt;"</span>, <span class='s'>"&gt;"</span>, <span class='nv'>x</span><span class='o'>)</span>
  <span class='nv'>x</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/grep.html'>gsub</a></span><span class='o'>(</span><span class='s'>"&amp;amp;"</span>, <span class='s'>"&amp;"</span>, <span class='nv'>x</span><span class='o'>)</span>
  <span class='nv'>x</span>
<span class='o'>&#125;</span>
<span class='nv'>html</span> <span class='o'>&lt;-</span> <span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/read_xml.html'>read_html</a></span><span class='o'>(</span><span class='nf'>unescape_html</span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/base/character.html'>as.character</a></span><span class='o'>(</span><span class='nv'>html</span><span class='o'>)</span><span class='o'>)</span><span class='o'>)</span>
<span class='nf'>xml2</span><span class='nf'>::</span><span class='nf'><a href='http://xml2.r-lib.org/reference/write_xml.html'>write_html</a></span><span class='o'>(</span><span class='nv'>html</span>, <span class='s'>"example.html"</span><span class='o'>)</span></code></pre>

</div>

Now we can look at the [example](example.html) and see that it has indeed some math! It's probably lacking fonts, the absence of any MathJax JS means that we see the math twice, one of them being a div for assistive technology.

## Conclusion

In this post I explored server-side MathJax rendering. I have not created a workable solution : An actually acceptable solution would necessitate more knowledge of MathJax, be far more detail-oriented and would check the accessibility of the produced document! Nevertheless, it was interesting to me to use chromote to extract HTML produced by MathJax and to learn more about MathJax. I don't think I'll feel like writing more LaTeX than now but I am sure curious to also check out MathJax "competitors" like KaTeX.[^4]

[^1]: MathJax is not the only way to render math for the web but it is the most popular one.

[^2]: I found the video [Accessible Math on the Web: A Server/Client Solution](https://www.writethedocs.org/videos/na/2016/accessible-math-on-the-web-a-server-client-solution-tim-arnold/) interesting, albeit a few years old already. Yes, I watched a video about SAS documentation.

[^3]: One could also choose to use [crrri](https://github.com/RLesur/crrri) but this is left as an exercise to the reader.

[^4]: If you use Katex you might be interested in a [related PR to rmarkdown](https://github.com/rstudio/rmarkdown/pull/1940).

