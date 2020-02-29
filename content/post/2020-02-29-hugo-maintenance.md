---
title: "What to know before you adopt Hugo/blogdown"
date: '2020-02-29'
tags:
  - admin
  - hugo
slug: hugo-maintenance
---

Fancy (re-)creating your website using Hugo, with or without blogdown?
Feeling [a bit anxious](https://twitter.com/OscarBaruffa/status/1233133764282322945)?
This post is aimed at being the Hugo equivalent of _"What to know before you adopt a pet"_.
We shall go through things that can/will break in the future, and what you can do to prevent future pain.

_I'm writing this post with R users in mind, which means I shall use R analogies and mentioning `blogdown`, 
but I hope some aspects are generalizable to other potential Hugo adopters._
_Some aspects are specific to deploying your website using git and an online git platform such as GitHub._

# Why Hugo/blogdown?

If you're reading this you've probably heard of Hugo somewhere.
It's getting quite popular in the R community thanks to the `blogdown` package, whose associated book features an [excellent intro to why Hugo (and `blogdown`)](https://bookdown.org/yihui/blogdown/static-sites.html).

I myself have used Hugo for this website (with a bit of `blogdown`) and dived into more details whilst [working on tweaks to the rOpenSci website](https://ropensci.org/technotes/2019/01/09/hugo/).
I really enjoy working with this website generator, partly because it's fast and open-source[^1].

# What can break or evolve?

When you have a website created with Hugo, there are two to four main actors:

* Hugo itself which you could think of as R;

* A Hugo _theme_ which defines the layout and style of your website. You could think of the theme as an R package. Your theme is a folder inside your website folder. You need one, no matter how minimal.

* the other things your website theme might depend on, such as Font Awesome.

* potential fourth actor: your tweaks to the theme which might be your editing an R package source; or maintaining a script/a second package on to of that R package to add some syntactic sugar or so to the existing functions of the package.

So what's going to break and evolve?

* Just like R packages need to keep up with changes to R sometimes, Hugo themes need to keep up with Hugo changes. Hugo is a trustworthy framework, that keeps improving, which however means sometimes removing support for some things or ways of doing them. Example: [this commit to the theme I'm using made a small change for making the theme compatible with Hugo>0.57](https://github.com/yoshiharuyamashita/blackburn/commit/123ebe8bb4fd3708fc51dab42613e6a3a7d37d4c). 

* Just like R packages need to keep up with changes to their external dependencies (web APIs, data format standard), Hugo themes need to keep with changes to external dependencies (font providers, JS scripts, etc). Example: [a commit to the theme I'm using that updates the templates to use the new syntax for Font Awesome icons](https://github.com/yoshiharuyamashita/blackburn/commit/fef095af788816dbc27f040ca98eee3df6b60c1c).

* Just like R packages often improve with time, Hugo themes evolve. And just as you might like updating packages to get the best new tools, you might like getting the fancy features offered by a new version of your theme. For instance look at [this releases note for some version of Hugo academic theme](https://sourcethemes.com/academic/updates/v4.6.0/): there are bug fixes but also cool improvements!

* Just like your taste or needs of R packages in your script might evolve, e.g. you might want to update old scripts to use `data.table` instead of `dplyr`, you might even want to switch themes!

# How to reduce the likelihood of breakages?

## Choose your theme wisely and keep in touch!

When choosing a theme i.e. a collection of layouts for your website, 
you'll have aesthetics and practicalities in mind.
E.g. if you're a prolific blogger you'll want the posts to be quite prominent.
As the blogdown book mentions, [also look at the theme's popularity and activity](https://bookdown.org/yihui/blogdown/themes.html) before adopting it.
This way you can have more trust in the theme's responding to Hugo changes and to bug reports.

Now, you'll have to know when the theme gets updated. How?

* You could go hardcore git and make the theme [a git submodule](https://git-scm.com/book/en/v2/Git-Tools-Submodules) of your website repo.

* You could [watch i.e. subscribe to the GitHub releases of that theme](https://help.github.com/en/github/receiving-notifications-about-activity-on-github/watching-and-unwatching-releases-for-a-repository), or, if the maintainer(s) use a less formal workflow, watch all activity from that repo.

* Any other idea? Maybe involving GitHub Actions, Dependabot, something else? 
Maybe just a reminder to have a coffee date with your theme repo once in a while?

## What if your theme gets orphaned?

What if you chose your theme wisely but it lost its maintainer(s)? 
In that case you'll need to look into adopting it or rolling out your own version, or changing themes.

## Make well-defined tweaks to the theme

Although you've adopted a theme, you'll probably want to personalize it a bit.
If you do so, do it with a good file structure and documentation hygiene!
As [very well explained in the `blogdown` book](https://bookdown.org/yihui/blogdown/custom-layouts.html), 

* some tweaks are directly supported by the theme via the website config file (think of it as an R function parameters) e.g. adding your name to the homepage rather than Jane Doe's;

* some tweaks require your writing layout files (think of it as writing a wrapper for an R package/re-writing your own version of some functions) e.g. adding some fun sentence to the footer which the original theme doesn't support. In this case you should store your custom layouts, like the fancy footer partial template, in a folder called `layouts/` at the root of your website folder; _not_ in the theme folder. [Hugo will give priority to `layouts/` stuff when defined](https://gohugo.io/templates/lookup-order/), to use them on top of theme stuff; and you'll easily see what you changed. Your future self will probably find much joy in your present self's documenting the why and how of your custom layouts in some sort of developer notes. The more custom layouts you write, the greater your future responsability.

_If you define CSS and JS files, they'd live in `static/` and be referred to in the website config file._

## IMHO Have your content as Markdown, not html files

With `blogdown` you can use .Rmd, .RMarkdown or .md as your website source, refer to [this exhaustive and clear comparison](https://bookdown.org/yihui/blogdown/output-format.html).
I am strongly in favour of never using .Rmd in a blogdown site because its output is an html file, not a .markdown/.md file. html files seem less portable to me. I like the idea of being able to take my Markdown content and rather easily move them to a new theme (or even a new framework, like when I migrated this website [from Jekyll](https://bookdown.org/yihui/blogdown/from-jekyll.html)).

Now, as explained in the table mentioned above, using .RMarkdown/.md has its limitations.
For html widgets I can recommend looking at [the setup Steph Locke created for the RECON learn website](https://www.reconlearn.org/post/tutorial-post-creation.html#html-widgets-plotly-leaflet).

## Update your theme

So if you've tweaked cleanly, you can update your theme when needed!
If you work with version control, and [you probably should](https://happygitwithr.com/), make a branch and work on the update in that branch.
If you don't, backup your website's current state first.

To "update your theme", you need to replace the theme folder of your website folder with the new theme files. 

You could do update the theme manually, or use `blogdown::install_theme()` with `force = TRUE`.

Build your website, look at what needs to be changed:

* maybe the config parameter names changed (like an R function which would have renamed one of the parameters);

* your custom layout might need some work too.

To figure out what needs to be changed, you'll probably want to read the changelog (or commit history) of your theme, and maybe even Hugo changelog.

Often, changes in your theme, and work needed on your website, won't be dramatic: the theme folder update, maybe one config parameter, a few lines diff in your custom layouts.

## Follow Hugo news?

If you wrote no custom layouts and use a very well maintained theme, you might never need to keep up with Hugo changes yourself.
However, if you've written Hugo themes, or strive to become a contributor to your theme, you might want to read [Hugo changelogs](https://gohugo.io/news/), follow [Hugo's source repository](https://github.com/gohugoio/hugo), or [Hugo's Twitter account](https://twitter.com/GoHugoIO), etc.

## Don't live on the edge

If you have a workflow on a continuous integration system updating your website every day from an external data source, [like Noam Ross does](https://www.noamross.net/2019/08/09/a-new-website/) (yes that's a very cool and very fancy setup!), use a specific Hugo version there, don't let the workflow install Hugo's latest version because it could break your website without your noticing. 
Update Hugo with intent, after reading a bit and/or testing it doesn't break your website.

## What if I just never update Hugo or my theme?

No, it's not a good solution in my opinion.
Never updating Hugo (neither locally nor on say Netlify) nor your theme probably means your website will still build as it used to.
However, updates to Hugo/themes contain both improvements and **bug fixes** so it's better to know you'll probably update the tools at least once in a while.

# Quick fixes to bad news

Imagine you made yourself a pretty website to showcase your cool posts and informative slidedecks.
In the meantime, you changed laptops and today Saturday you want to post the link to your talk from last week,
 before you head out for a barbecue with friends.
Your laptop doesn't have Hugo installed so you install its latest version using `blogdown::install_hugo()`, add your talk page, build the website, look at the preview and... notice it all looks wonky! Your website is broken!
If you remember the beginning of this post, what happened is probably an update in Hugo breaking your theme version. You'll eventually need to update your theme.

But you don't have time right now before your barbecue to do that, let alone to learn how to do that if it's the first time, so what can you do apart from not posting your talk content?

## If your site is deployed by gh-pages

I.e. your build your website locally and then push the rendered content to a gh-pages branch of a GitHub repo, you need to retrograde Hugo before doing that. Note that `blogdown::install_hugo()` has a `version` argument, refer to Hugo changelogs to see what version you had last used.

## If your site is deployed by Netlify

If you're lucky, you can just push your content, and since the Hugo version of your Netlify's config file hasn't changed, your website will build smoothly. You could make a PR to your own website to get a preview there before merging.

## Later

Eventually, one day soon, get to updating Hugo again, looking at your theme's changes, if needed extracting your tweaks from the theme folder if you made them there (I've done that, and now I sure stick to using the `layouts/` folder as mentioned earlier). 
Use some of the tips of the beginning of the post to reduce the likelihood of the scenario happening again.


# Conclusion

In this blog post I presented what the maintenance of a Hugo website entails in my experience.
I haven't mentioned other possible sources of breakage that are not specific to Hugo:

* [URLs in the content getting obsolete](https://ropensci.org/technotes/2019/12/19/urls-tidying/);

* Your changing deploy workflows (so use your own domain instead of a Netlify's one?);

* Your website source disappearing (so use backups);

* etc.

Coming back to Hugo, if you encounter problems I'd unsurprisingly recommend [Hugo docs](https://gohugo.io/documentation/) and [Hugo forum](https://discourse.gohugo.io/).

Now, if this all sounds overwhelming, I don't think these tech skills are harder than R skills but time is a limited resource so maybe you could outsource some of your website's creating and maintenance.
Maybe you can hire someone, or do a skill swap?

Don't hesitate to share your own experience and advice on maintaining Hugo websites!

[^1]: Also because I love the font of [its docs website](https://gohugo.io/documentation/) and looking at pages full of curly braces.