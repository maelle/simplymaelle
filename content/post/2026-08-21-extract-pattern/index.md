---
title: "Learning how to extract string patterns"
date: '2026-08-21'
slug: extracting-string-patterns
output: hugodown::hugo_document
tags:
  - useful functions
rmd_hash: a692a40cd589b1cf

---

This week I took time to re-read The Programmer's Brain by Felienne Hermans. Among the many gems one idea that stuck with me is that not learning how to do something and looking it up every time will make you less efficient. Therefore I starting feeling bad about one particular thing I never get done on my own: extracting parts of a string, even in simple cases.

## Avoidance tactics

For instance, how do you extract names from the following templated sentences?

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nv'>sentences</span> <span class='o'>&lt;-</span> <span class='nf'><a href='https://rdrr.io/r/base/c.html'>c</a></span><span class='o'>(</span></span>
<span>  <span class='s'>"My name is Moomin."</span>,</span>
<span>  <span class='s'>"My name is Little My."</span>,</span>
<span>  <span class='s'>"My name is Snork Maiden."</span></span>
<span><span class='o'>)</span></span></code></pre>

</div>

I would use either one of these two tactics:

1.  Removing the final period, and the first words.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>sub</a></span><span class='o'>(</span><span class='s'>"My name is "</span>, <span class='s'>""</span>, <span class='nf'><a href='https://rdrr.io/r/base/grep.html'>sub</a></span><span class='o'>(</span><span class='s'>".$"</span>, <span class='s'>""</span>, <span class='nv'>sentences</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "Moomin"       "Little My"    "Snork Maiden"</span></span>
<span></span></code></pre>

</div>

1.  Looking up the regex syntax online (maybe even dutifully reading the [stringr cheatsheet](https://rstudio.github.io/cheatsheets/html/strings.html#look-arounds)) or via a LLM. This could get me code calling stringr:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_extract.html'>str_extract</a></span><span class='o'>(</span><span class='nv'>sentences</span>, <span class='s'>"My name is (.*)."</span>, group <span class='o'>=</span> <span class='m'>1</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "Moomin"       "Little My"    "Snork Maiden"</span></span>
<span></span><span><span class='c'># Look arounds</span></span>
<span><span class='nf'>stringr</span><span class='nf'>::</span><span class='nf'><a href='https://stringr.tidyverse.org/reference/str_extract.html'>str_extract</a></span><span class='o'>(</span><span class='nv'>sentences</span>, pattern <span class='o'>=</span> <span class='s'>'(?&lt;=My name is ).+(?=\\.)'</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "Moomin"       "Little My"    "Snork Maiden"</span></span>
<span></span></code></pre>

</div>

Or some base R code:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/regmatches.html'>regmatches</a></span><span class='o'>(</span></span>
<span>  <span class='nv'>sentences</span>,</span>
<span>  <span class='nf'><a href='https://rdrr.io/r/base/grep.html'>regexpr</a></span><span class='o'>(</span><span class='s'>"(?&lt;=My name is ).+(?=\\.)"</span>, <span class='nv'>sentences</span>, perl <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "Moomin"       "Little My"    "Snork Maiden"</span></span>
<span></span></code></pre>

</div>

## My problems

Really I had two problems preventing me from being really autonomous:

- Not knowing enough regex.
- Not knowing where to put the regex, for whatever reason I felt I had to choose between adding a dependency on stringr or using the complicated two-step regexpr/regmatches syntax.

## Solutions

To solve the first problem (lack of regex knowledge), I need to be more intentional about remembering the look-arounds syntax for instance, or what a group is.

What solved my second problem (thinking I had to choose between a dependency or code distateful to me) was a very simple tip by my [rOpenSci colleague Jeroen Ooms](https://jeroen.github.io/): using [`sub()`](https://rdrr.io/r/base/grep.html)! The code below replaces the sentences with the names (capture groups) in them.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/grep.html'>sub</a></span><span class='o'>(</span><span class='s'>"My name is (.*)."</span>, <span class='s'>"\\1"</span>, <span class='nv'>sentences</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] "Moomin"       "Little My"    "Snork Maiden"</span></span>
<span></span></code></pre>

</div>

This is code he seems to use [quite often](https://github.com/search?q=%2Fsub.*%5C%5C1%2F+user%3Ajeroen+path%3A*.R&type=code&ref=advsearch)[^1].

What was especially great about this tip, beside its timing when I was reading the book, is that it made me "get" groups more easily. The group is what's between parentheses, it's not more complicated than that (at least I don't need to know more right now).

## Conclusion

I will try to keep mindful of not being too lazy to learn some things when I can actually learn them. And now I know that I can use something else than stringr or the not so easy base R syntax with [`regmatches()`](https://rdrr.io/r/base/regmatches.html): a simple call to [`sub()`](https://rdrr.io/r/base/grep.html)! Watch me win seconds every time I have to extract parts of a string. :grin:

[^1]: Learning how to add [regex to code search on GitHub](https://docs.github.com/en/search-github/github-code-search/understanding-github-code-search-syntax#using-regular-expressions) was well worth the small effort.

