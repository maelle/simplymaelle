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
such as going from "I'm just gonna add the link to my recent slidedeck to my webpage" to "OMG my website is broken" in a few short minutes on a sunny week-end afternoon.
I'm writing this post with R users in mind, which means I'm using R analogies and mentioning `blogdown`, 
but I hope some aspects are generalizable to other potential Hugo adopters.

# Why Hugo/blogdown?

If you're reading this you've probably heard of Hugo somewhere.
It's getting quite popular in the R community thanks to the `blogdown` package, whose associated book features an [excellent intro to why Hugo (and `blogdown`)](https://bookdown.org/yihui/blogdown/static-sites.html).

I myself have used Hugo for this website (with a bit of `blogdown`) and dived into more details whilst [working on tweaks to the rOpenSci website](https://ropensci.org/technotes/2019/01/09/hugo/).
I really enjoy working with this website generator, maybe because it's fast and open-source, 
maybe because I love the font of [its docs website](https://gohugo.io/documentation/) and looking at pages full of curly braces.

# What can break?

When you have a website created with Hugo, there are two to four main actors:

* Hugo itself which you could think of as R;

* your website _theme_ which defines its layout, that you could think of as an R package. Your theme is a folder inside your website folder.

* the other things your website _theme_ might depend on, such as Font Awesome.

* potential fourth actor; your tweaks to the theme which might be your editing an R package source; or maintaining a script/a second package on to of that R package to add some syntactic sugar or so to the existing functions of the package.

So what's going to break?

* Just like R packages need to keep up with changes to R sometimes, Hugo _themes_ need to keep up with Hugo changes. Hugo is a trustworthy frameworks, that keeps improving, which however means sometimes removing support for some things or ways of doing them.

Example: [this commit to the theme I'm using](https://github.com/yoshiharuyamashita/blackburn/commit/123ebe8bb4fd3708fc51dab42613e6a3a7d37d4c) whose message is _"Using only .Data.Pages will only show a link to the Post page in the home page. Using .Site.RegularPages returns the homepage to a normal showcase of existing posts. This change happened starting at Hugo >0.57."_. 

* Just like R packages need to keep up with changes to their external dependencies (web APIs, data format standard), Hugo _themes_ need to keep with changes to external dependencies (font providers, JS scripts, etc). Example: [a commit to the theme I'm using that updates the templates to use the new syntax for Font Awesome icons](https://github.com/yoshiharuyamashita/blackburn/commit/fef095af788816dbc27f040ca98eee3df6b60c1c).

# How to reduce the likelihood of breakages?

## Choose your theme wisely and keep in touch!

When choosing a theme i.e. a collection of layouts for your website, 
you'll have aesthetics and practicalities in mind.
E.g. if you're a prolific blogger you'll want the posts to be quite prominent.
As the blogdown book mentions, [also look at the theme's popularity and activity](https://bookdown.org/yihui/blogdown/themes.html) before adopting it.
This way you can have more trust in the theme's responding to Hugo changes.

Now, you'll have to know when the theme gets updated. How?

* You could go hardcore git and make the theme a git submodule of your website repo.

* You could [watch i.e. subscribe to the GitHub releases of that theme](https://help.github.com/en/github/receiving-notifications-about-activity-on-github/watching-and-unwatching-releases-for-a-repository), or, if the maintainer(s) use a less formal workflow, watch every activity from that repo.

* Any other idea? Maybe involving GitHub Actions, Dependabot, something else? 
Just a reminder to have a coffee date with your theme repo once in a while?

## Make well-defined tweaks to the theme

Although you've adopted a theme, you'll probably want to personalize it a bit.
As [very well explained in the `blogdown` book](https://bookdown.org/yihui/blogdown/custom-layouts.html), 

* some tweaks are directly supported by the theme via the website config file (think of it as an R function parameters);

* some tweaks require your writing layout files (think of it as writing a wrapper for an R package/re-writing your own version of some functions). In this case you should

## Update your theme

```r
blogdown::install_theme("yihui/hugo-xmin", update_config = FALSE, theme_example = FALSE, force = TRUE)
```

# Follow Hugo news?

If you wrote no custom layouts and use a very well maintained theme, you might never need to keep up with Hugo changes yourself.
However, if you've written Hugo themes, or strive to become a contributor to your theme, you might want to read [Hugo changelogs](https://gohugo.io/news/), follow [Hugo's source repository](https://github.com/gohugoio/hugo), or [Hugo's Twitter account](https://twitter.com/GoHugoIO), etc.