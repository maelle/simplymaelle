---
title: "What to know before you adopt Hugo/blogdown"
date: '2020-03-01'
tags:
  - admin
  - hugo
slug: hugo-maintenance
---

Fancy (re-)creating your website using Hugo, with or without blogdown?
Feeling [a bit anxious](https://twitter.com/OscarBaruffa/status/1233133764282322945)?
This post is aimed at being the Hugo equivalent of _"What to know before you adopt a pet"_.
We shall go through things that can/will break in the future, and what you can do to prevent horrible scenarios 
such as going from "I'm just gonna add the link to my recent slidedeck to my webpage" to "OMG my website is broken".
I'm writing this post with R users in mind, which means I'm using R analogies and mentioning `blogdown`, 
but I hope some aspects are generalizable to other potential Hugo adopters.

# Why Hugo/blogdown?

If you're reading this you've probably heard of Hugo somewhere.
It's getting quite popular in the R community thanks to the `blogdown` package, whose associated book features an [excellent intro to why Hugo (and `blogdown`)](https://bookdown.org/yihui/blogdown/static-sites.html).

I myself have used Hugo for this website (with a bit of `blogdown`) and dived into more details whilst [working on tweaks to the rOpenSci website](https://ropensci.org/technotes/2019/01/09/hugo/).
I really enjoy working with this website generator, maybe because it's fast and open-source, 
maybe because I love the font of its docs website and looking at pages full of curly braces.

# What can break?

When you have a website created with Hugo, there are two to four main actors:

* Hugo itself which you could think of as R;

* your website _theme_ which defines its layout, that you could think of as an R package.

* the other things your website _theme_ might depend on, such as Font Awesome.

* potential fourth actor; your tweaks to the theme which might be your editing an R package source; or maintaining a script/a second package on to of that R package to add some syntactic sugar or so to the existing functions of the package.

So what's going to break?

* Just like R packages need to keep up with changes to R sometimes, Hugo _themes_ need to keep up with Hugo changes. 

Example: [this commit to the theme I'm using](https://github.com/yoshiharuyamashita/blackburn/commit/123ebe8bb4fd3708fc51dab42613e6a3a7d37d4c) whose message is _"Using only .Data.Pages will only show a link to the Post page in the home page. Using .Site.RegularPages returns the homepage to a normal showcase of existing posts. This change happened starting at Hugo >0.57."_. 

* Just like R packages need to keep up with changes to their external dependencies (web APIs, data format standard), Hugo _themes_ need to keep with changes to external dependencies (font providers, JS scripts, etc). Example: [a commit to the theme I'm using that updates the templates to use the new syntax for Font Awesome icons](https://github.com/yoshiharuyamashita/blackburn/commit/fef095af788816dbc27f040ca98eee3df6b60c1c).