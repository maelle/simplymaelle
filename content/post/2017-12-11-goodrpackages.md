---
title: How to develop good R packages (for open science)
date: '2017-12-11'
slug: 2017/12/11/goodrpackages
comments: yes
---


I was invited to [an exciting ecology & R hackathon](https://methodsblog.wordpress.com/2017/11/16/hackathon-challenges/) in my capacity as a co-editor for [rOpenSci onboarding system of packages](https://github.com/ropensci/onboarding). It also worked well geographically since this hackathon was to take place in Ghent (Belgium) which is not too far away from my new city, Nancy (France). The idea was to have me talk about my "top tips on how to design and develop high-quality, user-friendly R software" in the context of open science, and then be a facilitator at the hackathon. 

The talk topic sounded a bit daunting but as soon as I started preparing the talk I got all excited gathering resources -- and as you may imagine since I was asked to talk about *my* tips I did not need to try & be 100% exhaustive. I was not starting from scratch obviously: we at rOpenSci already have [well-formed opinions](https://github.com/ropensci/onboarding/blob/master/packaging_guide.md) about such software, and I had given a talk about [automatic tools for package improvement](http://www.masalmon.eu/2017/06/17/automatictools/) whose content was part of my top tips. 

As I've done in the past with my talks, I chose to increase the impact/accessibility of my work by sharing it on this blog. I'll also share this post on the day of the hackathon to provide my audience with a more structured document than my slides, in case they want to keep some trace of what I said (and it helped me build a good narrative for the talk!). Most of these tips will be useful for package development in general, and a few of them specific to scientific software.

<!--more-->

# What is a good R package in my opinion?

The answer to this will appear in the blog post but if I had to say this in one sentence, I'd say that a good R package for open science is an useful, robust and well-documented package that's easy to find and that users will trust.

# How to plan your R package

## Should your package even exist?

You probably want to write and publish an R package because you have some sort of scripts or functions that you think would be useful to other people, or you do not even have that but wish the functionality existed. I won't speak about the [benefits of building an R package for yourself](http://r-pkgs.had.co.nz/intro.html), or a [research compendium](https://github.com/ropensci/unconf17/issues/5) to make your work reproducible. 

I think you should ask yourself these questions before starting to code away:

* Will this piece of software really be useful to someone else than you? If not, building a package will still save you time, but you maybe don't need to put a lot of effort into it. If you doubt, you can ask around you if other people would be interested in using it. Brainstorming package ideas is part of what happened before the hackathon, in a collaborative document.

* Is your idea really new, or is there already a package out there performing the exact same task? 

* If there is one, would your implementation bring something to the market, e.g. an user-friendlier implementation? There are for instance two R packages that interface the Keras Deep Learning Library, [`keras`](https://keras.rstudio.com/) and [`kerasR`](https://github.com/statsmaths/kerasR). In some cases having several packages will be too much, in some cases it won't because one package will suit some users better. 

* If there is already a similar package, should you work on your own or collaborate with the author(s) of the original package? The `rtweet` package actually completely replaced the `twitteR` package after the two maintainers agreed on it. I guess the best thing is to just ask the maintainer, unless their contributing guide already makes clear what's welcome and what's not (more on contributing guides later!).

## What should your package contain exactly?

Then, once you've really decided that you want to write and publish this R package, you could start planning its development. Two things seem important to me at this point:

* Make your package compatible with the workflow of your users. For instance, if your package has something to do with time series, it's probably best to try and make your output compatible with time series classes people are used to, and for which there are other packages available.

* Do not get too ambitious. For instance, do you want to have a specific plotting method for your output, or can you rely on your users' using say `ggplot2`, merely giving them an example of how to do so in a vignette? I've heard that's part of the [Unix philosophy](https://en.wikipedia.org/wiki/Unix_philosophy), small pieces building on top each other. This will save you time, and let you concentrate on the novel and important piece you're providing to the community.

Once you've decided on all of this, maybe sketch a to-do list. If you're already familiar with Github, which you will be after reading some books recommended in the next section, you can have one issue per item, or even take advantage of [Github's milestones feature](https://guides.github.com/features/issues/). You don't need to have a master plan for your whole package life, because one can hope your users will also influence the future features, and well you'll keep getting smarter as time goes by and the ideas will keep flowing. 

If you want to work on your package as a team, make roles clear from the beginning, e.g. who will be the maintainer and keep others right on track... in particular if you start the project as a hackathon, who will make sure development goes forward once everyone goes home?

## What will your package be called?

Last but not least in this planning phase, you should call your package something! I'd advise names in lower case as recommended by rOpenSci, because this way your user doesn't need to remember where the capital letters are (yes, it is a funny recommendation by an organization called rOpenSci), and because a full capital letter name looks like screaming. I recommend checking your package name via the [`available` package](https://github.com/ropenscilabs/available) which will tell you whether it's valid, available on the usual packages venues and also whether it has some unintended meanings.

# How to build an R package

Here I'll list useful resources to learn how to build an R package while respecting best practice. When googling a bit about R package development, I was surprised to see that although there are a ton of R packages out there, there aren't that many guides. Maybe we do not need more than CRAN official guide after all, and [Hadley Wickham's book](http://r-pkgs.had.co.nz/) is excellent, especially with the corresponding RStudio and `devtools` magic combo... but well diversity of resources is good too! 

I'd recommend your building packages in RStudio making the most of automatic tools like `devtools`. Note, in comparison to the content of Hadley's book, some automation is now [supported by `usethis`](https://www.tidyverse.org/articles/2017/11/usethis-1.0.0/).

Using these short and long form resources will help you increase the quality of your package. It'll have the structure it should have for instance.

## Short tutorials

To learn the 101 of package building, check out [Hilary Parker's famous blog post *Writing an R package from scratch*](https://hilaryparker.com/2014/04/29/writing-an-r-package-from-scratch/) that got many R package developers started (I've for instance seen quite a few tweets of people celebrating their first R package and thanking Hilary!) or [this pictorial by Matthew J Denny](http://www.mjdenny.com/R_Package_Pictorial.html). How someone can be patient enough to do so many screenshots is beyond me but it's really great that he did!

## Books

Then you can read books! I've mentioned Hadley's book. There's also a very good one called [*Mastering Software Development in R* by Roger D. Peng, Sean Kross, and Brooke Anderson](https://bookdown.org/rdpeng/RProgDA/). And I'd be damned if I do not mention the bible a.k.a [*Writing R extensions*, the official CRAN guide](http://cran.r-project.org/doc/manuals/r-release/R-exts.html) which is not really a book but well a (very) long form resource. In all cases you can cherry pick what you need and want to read but be sure you follow the rules when submitting a package to CRAN for instance. 

## MOOCs

If you're more into MOOCs,  there is a [Coursera specialization corresponding to the book by Roger Peng, Sean Kross and Brooke Anderson](https://fr.coursera.org/specializations/r), with a course specifically about R packages.

## In-person training

You can also look for in person training! Look up your local R user group or R-Ladies chapter if there's one, maybe book a company, etc. If you happen to be in or near South Africa in March 2018, I'll be teaching [a workshop about R package development in Cape Town at satRday 2018 on March the 16th](http://capetown2018.satrdays.org/#workshops), maybe you can buy a ticket and come!

## More precise guidelines

If you want to follow even more precise guidelines than the ones in the resources, check out the [rOpenSci packaging guide](https://github.com/ropensci/onboarding/blob/master/packaging_guide.md). 

# Where should your package live?

## Open your code!

I was giving a talk in the context of *open* science so this one was obvious. And R is an *open-source* language, so if you publish your package there's no black box. On top of that you should make the place where your code is created and modified open to facilitate its browsing, bug reports (if they are open, there's less chance they'll be duplicated, and people can see how fast/whether you respond to them), and collaboration.

I've mentioned Github when giving my planning tips earlier. My recommendation is to have the code of your R package live in an open repository with version control. That might be R-Forge and svn if you like, or Github/Gitlab and git. I was introduced to version control basics with svn and R-Forge and found that the small tortoise of the Windows Tortoise svn software was cute, that's all I can tell you about svn, ah! I then learnt git and Github thanks to [this part of Hadley's book](http://r-pkgs.had.co.nz/git.html), which also gives you good reasons to use git and Github. I've heard good things about [this online book by Jenny Bryan](http://happygitwithr.com/) and [this website for when bad things happen to you](http://ohshitgit.com/).

## Put your package in one of the official platforms

That is, CRAN or Bioconductor, where they're easier to install, and also for the badge of trust it gives your package, although you might already follow stricter guidelines than these platforms.

By the way regarding trust, [this blog post by Jeff Leek, *How I decide when to trust an R package*](https://simplystatistics.org/2015/11/06/how-i-decide-when-to-trust-an-r-package/) is an interesting read.

Special tip specific to CRAN, release your package using `devtools::release()` which will help you catch some issues before submitting your package to CRAN, and will allow you to do the whole submission without even leaving R.

## Link the two 

I wrote this with my CRAN experience in mind, but this is probably relevant for Bioconductor too. 

I want to underline one point: please indicate the URL of your code repository and the link to the issues or bug reports page in the DESCRIPTION of your package. This way, once your package is up on CRAN for instance, users can trace it back more easily. Really, do not forget to do that. Also indicate the link to your issue page in DESCRIPTION under BugReports.

Another important point is that if your package is on CRAN, and your code repository on Github, it's good to:

* [create a release](https://help.github.com/articles/creating-releases/) and attach the corresponding NEWS each time you upload a new version on CRAN (otherwise your release is a surprise release). One reason for having releases is archiving, this way users can download and install a given version quite easily. Another one is that users might use tools such as [Sibbell](https://about.sibbell.com/) to be notified of new releases.

* have a [CRAN badge](https://www.r-pkg.org/services#badges) in the README, see [this README for instance](https://github.com/ropensci/ropenaq). It's easier to install packages from CRAN so people might want to do that, they can see what the latest CRAN version number is, and well you recognize the hosting on CRAN in a way, because you should be thankful for CRAN work.

# How to check your package automatically

Here I will assume your package has [unit tests](http://r-pkgs.had.co.nz/tests.html) and is hosted on Github, although the latter isn't a condition for all tips here. If you've read [my blog post about automatic tools for improving packages](http://www.masalmon.eu/2017/06/17/automatictools/) thoroughly, this will sound quite familiar, and I'd advise you to go to this post for more details.

These tools are all here to help you get a better package before releasing it.

## R CMD check

Use [`devtools::check()`](http://r-pkgs.had.co.nz/check.html)! The link I've put here explains why you should, and the efforts you should put in solving the different messages it'll output.

## Continuous integration

As a complement to the last sub-section, if your code lives on say Github, you can have R CMD check run on an external platform each time you push changes. This is called continuous integration and will help you spot problems without needing to remember to do the checks. Furthermore, having them run on an external platform allows you to choose the R version so you can check your package works with different R versions without installing them all.

[This blog post by Julia Silge](https://juliasilge.com/blog/beginners-guide-to-travis/) is an excellent introduction to continuous integration for beginners.

To perform checks on say Windows only once in a while, you might want to check [the R-hub project](https://github.com/r-hub). Making sure your package works on several platforms is a condition for CRAN release, and in any case something you'll want to do to have an user-friendly package.

## Style your code

Style your code for future readers, who will be future you, collaborators and curious users. If your code is pretty, it'll be easier for them to concentrate on its content.

Use [`lintr`](https://github.com/jimhester/lintr) for static code analysis.

To prettify your code, use [`styler`](https://github.com/r-lib/styler).

## Get automatic advice about your package

Install and run [`goodpractice`](https://github.com/mangothecat/goodpractice) for “Advice (that) includes functions and syntax to avoid, package structure, code complexity, code formatting, etc.”.

## Remove typos!

Use the `devtools::spell_check()` function. Typos in documentation are not the end of the world but can annoy the user, and it's not hard to remove them thanks to this tool, so do it when you get a chance.

All these tools provide you with a list of things to change. This means they create a to-do list without your needing to think too much, so if you're a bit tired but want to improve your package, you can let them fix the agenda!

# Bring the package to its users and get feedback

These two aspects are linked because your users will help you make your package better for them, but for that to happen they need to know about your package and to understand it.

## Documentation!

You know how your package works but the users don't (and future you might forget as well!). Document your parameters, functions, add examples, and also write [vignettes](http://r-pkgs.had.co.nz/vignettes.html). At least one vignette will show your users how the different functions of your package interact.

At the beginning of this document I mentioned that when planning your package you should think about making it compatible with your users workflow. One of the vignettes can be an example of just that. Otherwise, it'll be hard for users to see how useful your package is... and when writing the example workflow, you might find your package lacks some functionality.

Once you've written all these documentation parts, create a website for your package using [`pkgdown`](http://hadley.github.io/pkgdown/). [This tutorial](http://enpiar.com/2017/11/21/getting-down-with-pkgdown/) is excellent.

## User-friendly programming

I'll give four tips here, partly inspired by [this essay by François Chollet](https://blog.keras.io/user-experience-design-for-apis.html). I want to share these two sentences from the essay with you: " In the long run, good design wins, because it makes its adepts more productive and more impactful, thus spreading faster than user-hostile undesign. Good design is infectious.".

* Have error and warning messages that are easy to understand. For instance, if communicating with an API, then translate http errors.

* Check the input. For instance, If a parameter provided to a function should be a positive number, check it is and if it is not, return a friendly error message.

* Offer shortcuts. For instance, if your function relies on an API key being provided, you can silently search for it in environment variables without the user doing anything, provided you explain how to store an environment variable in the documentation. This is how my [`opencage`](https://github.com/ropensci/opencage) works if you want an example. Noam Ross made the PR that added this functionality, and I agree that it makes the package user-friendler. Likewise, have good default values of other parameters if possible.

* Choose a good naming scheme that will allow your users to more easily remember the names of functions. You could choose to have a prefix for all the functions of your package, which is recommended by rOpenSci, and [discussed in this very interesting and nuanced blog post by Jon Calder](http://joncalder.co.za/2017-12-04-naming-things-is-hard/).

## Promotion 

Now that you have a well-documented error-friendly package... how are your end users going to learn that it exists? Note that you should have a good idea of your potential users when planning your package (or at least part of them, who knows who else might find it useful?). One way to define good venues for promotion is to think of how *you* learn about R packages, but as an R developer you're probably more into R and R news than your future average user! For information you can also see the results of [Julia Silge's survey about how people discover packages](https://juliasilge.com/blog/package-search/) [here](https://app.doopoll.co/poll/FGZqTL7vpaaCgpWCM/live-results).

Obvious venues for promotion are social media, for instance Twitter with the #rstats hashtag in the form of a carefully crafted tweet including a link and a cool image like a figure or screenshot and maybe emojis, but that might not be enough for your niche package. If you write an ecology package and you have a well-recognized blog in that area blog about your package of course. You could also ask someone to let you guest post on their well-recognized niche field blog. 

If your package is an interface to an existing tool or internet resource, try to have your package listed on that tool or resource webpage.

As a scientist, you can write a paper about your package and publish it to a journal well known in your field like [Methods in Ecology and Evolution](http://besjournals.onlinelibrary.wiley.com/hub/journal/10.1111/(ISSN)2041-210X/). By the way when you use R and packages in such a paper or any other paper do not forget to cite them (using the output of `citation()` for instance) to acknowledge their usefulness and help their authors feel their work is valued (furthermore if they're academics having their work cited is quite crucial for their career advancement).

## Review

As a scientist you're used to having papers peer-reviewed. In general if you submit a paper about your package, your paper will be reviewed more than your package, but you can actually get the benefits of peer-review for your package itself! This is a fantastic way to improve the quality and user-friendliness of your package.

Where to have your package reviewed?

* You don't need anything formal, maybe you can ask someone you know and who could for instance be an user of your package? You can ask them to review the code or its documentation or both, depending on their skills and time of course.

* [The R Journal](https://journal.r-project.org/) asks reviewers of papers to have a look at the package, as does [The Journal of Statistical Software](https://www.jstatsoft.org/index). That said, you'll need to write a whole paper to go with your package which is maybe something you don't want to.

* [The Journal of Open Science Software, JOSS](http://joss.theoj.org/) asks reviewers to check some points in your package and you to write a very minimal paper about your package. The review process is minimal but you can still get useful feedback.

* At [rOpenSci we review](https://www.numfocus.org/blog/how-ropensci-uses-code-review-to-promote-reproducible-science/) packages that fit in [our categories](https://github.com/ropensci/onboarding/blob/master/policies.md). Your package will be reviewed by two people. We have a partnership with the Journal of Open Source Software so that if your package fits in both venues, the review over at JOSS will be a breeze after the rOpenSci reviews. We also have a partnership with MEE, allowing a duo paper-package to be reviewed by MEE and rOpenSci respectively, see [this post](https://methodsblog.wordpress.com/2017/11/29/software-review/).

Needless to say, but I'll write it anyway, all these review systems work both ways... Offer your reviewing services! You'll actually learn so much that you'll find ideas to improve your own packages.

## Make your repo a friendly place

Another way to improve the quality of your package and make it more user friendly is to put people at ease when they have a question, a bug report, or a suggestion for improvement. Adding a code of conduct (use `devtools::use_code_of_conduct` as a starting point) and a contributing guide (with issue and PR templates if you want, in any case making clear what kind of contributions you welcome) to your package repository will help ensuring that. Also try to respond timeously to requests.

On top of having a contributing guide you could also add a [status badge](https://github.com/RMHogervorst/badgecreatr) for your repository so that any visitor might see at a glance whether your package is still in an early development stage.

If people actually start contributing to the package, try being generous with acknowledgements. A selfish reason is that it'll motivate people to keep contributing to your package. A less selfish reason is that it makes the whole R community more welcoming to new contributors. So you can add discoverers and solvers of bugs for instance as ctb in DESCRIPTION and add a line about their contribution in NEWS.md.

## Package analytics

If you're curious, you can get an idea of the number of downloads of your package via the [`cranlogs` package](https://cranlogs.r-pkg.org/). Using it you can compare your package popularity to similar packages, and check whether there was a peak after a course you've given for instance.

Similarly, if your package repository is hosted on Github, using the [`gh` package](https://github.com/r-lib/gh) you can do similar analyses, looking at the number of stars over time for instance.

# The end

This is the end of my list of tips, do not hesitate to add suggestions in the comments!


