---
title: "Three useful (to me) R notions"
date: '2023-07-24'
tags:
  - good practice
  - code style
slug: basic-patterns
output: hugodown::hugo_document
rmd_hash: eba3680fb009e07a

---

Following my recent post on [three useful (to me) R patterns](/2023/06/06/basic-patterns/), I've written down three other things on a tiny sticky note. This post will allow me to throw away this beaten down sticky note, and maybe to show you one element you didn't know?

## `nzchar()`: a fast way to find out if elements of a character vector are non-empty strings

One of my favorite testing technique is the escape hatch strategy, about which I wrote a [post on the R-hub blog](https://blog.r-hub.io/2023/01/23/code-switch-escape-hatch-test/): you make part of your code responsive to an environment variable, and you locally set that environment variable in your tests. In your code, to determine whether the environment variable has been set to something, anything, you can use the [`nzchar()`](https://rdrr.io/r/base/nchar.html) function.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='s'>"Hello World"</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='s'>""</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] FALSE</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='kc'>NA</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] TRUE</span></span>
<span></span><span><span class='nf'><a href='https://rdrr.io/r/base/nchar.html'>nzchar</a></span><span class='o'>(</span><span class='kc'>NA</span>, keepNA <span class='o'>=</span> <span class='kc'>TRUE</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; [1] NA</span></span>
<span></span></code></pre>

</div>

