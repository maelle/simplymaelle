---
title: Automatic tools for improving R packages
date: '2017-06-17'
tags:
  - package-development
  - pkgdown
  - lintr
  - goodpractice
  - devtools
  - available
  - meetup
slug: automatictools
comments: yes
---


On Tuesday I gave a talk at a meetup of the R users group of Barcelona. I got to choose the topic of my talk, and decided I'd like to expand a bit on [a recent tweet of mine](https://twitter.com/ma_salmon/status/824646744688488450). There are tools that help you improve your R packages, some of them are not famous enough yet in my opinion, so I was happy to help spread the word! I [published my slides online](http://rpubs.com/masalmon/282469) but thought that a blog post would be nice as well.

![](/figure/source/2017-06-17-automatictools/tweets.png)

<!--more-->

During my talk at RUG BCN, for each tool I gave a short introduction and then applied it to a small package I had created for the occasion. In that post I'll just shortly present each tool. Most of them are only _automatic_ because they _automatically_ provide you with a list of things to fix, but they won't do the work for you, sorry. If you have an R package you develop at hand, I'd really advise you to apply them on it and see what you get! I concentrated on tools improving the coding style, the package structure, testing, the documentation, but not features and performance.

Note: before my talk [Luca Cerone](https://twitter.com/lucacerone?lang=es) gave a short introduction to package development with `devtools` and `roxygen2`. This is how I develop packages now although the first package I contributed to, [`surveillance`](https://cran.r-project.org/web/packages/surveillance/index.html), has separate man pages, a NAMESPACE maintained by hand, etc. I gave its maintainer a hard time with all my stupid beginner mistakes! Thankfully Sebastian was always patient and nice. In any case, if you want to start developping packages you should definitely read [Hadley Wickham's book](http://r-pkgs.had.co.nz/), it's a good one! I read it all once I started upping my package game -- this upping being a continuous process of course.

# The basics: run R CMD check!

In his talk Luca said that when he develops a package he often doesn't have time to check it passes R CMD check, which made me die inside a little bit. I totally understand the lack of time but I think that R CMD check complains about important stuff, and most often unknown NOTE, WARNING or ERROR messages can be googled and solved, and one learns something. So please do run R CMD check!

![](/figure/source/2017-06-17-automatictools/runrcmdcheck.jpeg)

The R-Lady that uploaded this picture to the Meetup event told me that this is my "Run R CMD check, you fools!" face (she actually said _Usad el R CMD check, malditos!!!_). 

# `lintr`, Static Code Analysis for R

I then presented Jim Hester's `lintr` package which allows you to check some stuff in your code without running it. It will for instance tell you if you used camel case instead of snake case for a variable name, or if you defined a variable without using it afterwards. The README of the [Github repository of the package](https://github.com/jimhester/lintr) provides a list of available linters, each linter being one of these annoying, hum useful, checks.

How does one use `lintr`? Just by typing `lintr::lint_package()` at the root of the package folder. This week I applied it to a package of mine and spent some time adding white space that made my code prettier. In my opinion using `lintr` helps you maintain a code that is nice to read, which is cool because then you, future you or another human can concentrate on important stuff when reading it.

I mentioned two small tips as well.

## How to customize the `lintr` linters?

For that one needs to create a .lintr file at the root of the package (and add it to .Rbuildignore). Here is an example of the content of such a file:

```r
with_defaults(camel_case_linter = NULL, # you prefer camel case 
              snake_case_linter, # so flag snake case
              line_length_linter(120)) # you love long lines
```

## How to never ever ignore `lintr` output

You can add an unit test for `lintr`! Why would one ever want to do that? Well, you might want to force yourself, or contributors to your package, to respect some rules. The test would be:

```r
if (requireNamespace("lintr", quietly = TRUE)) {
  context("lints")
  test_that("Package Style", {
    lintr::expect_lint_free()
  })
}
```

After I published my slides [someone added such a test to one of his packages](https://twitter.com/najkoja/status/874889344888320000) which made me very proud.

One tip I forgot to mention during my talk is how to add exceptions, I was far too strict!

## How to exclude lines from `lintr` checks

For that either put `# nolint` at the end of the line or `# nolint start` and `# nolint end` before and after a block of lines you'd like to exclude. A good example is for instance a very long line that you can't break because it contains a very long URL. 

# `goodpractice`, Advice on R Package Building 

This package by Gabor Csardi can only be installed [from Github](https://github.com/mangothecat/goodpractice). It integrates a variety of tools, including `lintr`, for providing "Advice (that) includes functions and syntax to avoid, package structure, code complexity, code formatting, etc.". For instance, `goodpractice` uses [`cyclocomp`](https://github.com/mangothecat/cyclocomp) to check whether any of your functions is too complex. A participant of the meetup told me she got a lot of messages when running `goodpractice::gp()` on her first package, which is great because it means `goodpractice` will really have provided her with useful guidance. Some things that `goodpractice::gp()` will flag might be necessary features of your package, but in general it's a nice list to read. Note: you can only run `goodpractice::gp()` once R CMD check passes.

[Romain François](https://twitter.com/romain_francois?lang=es) who came to the Meetup from Montpellier (thanks, Romain!) asked me if I had ever run `goodpractice::gp()` on famous packages like `dplyr`. I haven't but since the default advice of `goodpractice` seems quite consistent with the advice in Hadley Wickham's book, I doubt I'd find anything interesting.

At [rOpenSci onboarding](https://github.com/ropensci/onboarding), we run `goodpractice::gp()` on every submitted package as part of the editorial checks, which makes me like this tool a bit more every week. It's actually tunable but the defaults are so great that I never needed to tune it!

I was asked when to run `goodpractice::gp()` and I'd say once in a while, e.g. before submitting a package to CRAN for the first time, or after creating its first working version. 

# `devtools::spell_check()`

This function was written by Jeroen Ooms and is supported by his [`hunspell` package](https://github.com/ropensci/hunspell). It will look for possible typos in the documentation of a package. In the output, there might be quite a few false positives, e.g. the name of an API, but I strongly believe that its helping you find typos in the documentation compensates the effort of reading the whole list of potential typos.

I've noticed that `devtools::release()`, which one can use when submitting a package to CRAN, now includes the output of `devtools::spell_check()`. It helped me find a typo at the last minute in one of my packages! Typos might not be that bad, but a typo-free documentation looks much better.

![](/figure/source/2017-06-17-automatictools/audience.jpeg)

## `pkgdown`, generate static html documentation for an R package

This last tool was the only one that's truly automatic. This package was created by Hadley Wickham and is currently only [on Github](https://github.com/hadley/pkgdown). It allows you to create a pretty website for your package without any big effort.

How does one use it? After installing it, run `pkgdown::init()` and `pkgdown::build_site()` at the root of your package. This will create a docs/ folder containing the static html documentation, that you can browse locally. 

Now, how do you present it to the world? In theory I guess you could publish it anywhere but the really handy thing is to have it as a Github page, if you develop your package on Github. For this you just need to change the settings of the repository to make master/docs the source for Github pages.

![](/figure/source/2017-06-17-automatictools/gh.png)

Unless you typically redirect all links from your Github account to your domain, your package will live at account_name.github.io/package_name. See for instance [this nice package of Dirk Schumacher's](https://github.com/dirkschumacher/ompr) whose website is [here](https://dirkschumacher.github.io/ompr/).

## How to customize the website?

This part is not automatic, and demands creating a `_pkgdown.yml` file at the root of your package. I've only mentioned two aspects:

* One can change the style of the website, e.g. [in this config file](https://github.com/maelle/rtimicropem/blob/master/_pkgdown.yml) I chose another Boostrap theme than the default one. To choose one Boostrap theme one can browse them online or look at a pkgdown website that one likes and look at how it was created. A participant of the meetup told me that there is an RStudio addin for choosing a theme but I couldn't locate it.

* One can change the order of vignettes, or of the functions, and even group them, which might be extremely useful when a package has many functions. Usually the alphabetical order makes little sense. See [here](https://github.com/dirkschumacher/ompr/blob/master/_pkgdown.yml) the config file of [this website](https://dirkschumacher.github.io/ompr/), the grouping is really well done.

# Conclusion

Since this was a talk, not a long workshop, and also because I don't know everything, I have not mentioned every useful tool in the world. I'd be grateful for suggestions in the comments, and in the meantime here are some tools I briefly mentioned at the end of my talk:

* Continuous integration, with this [well written post by Julia Silge](https://juliasilge.com/blog/beginners-guide-to-travis/)

* The [R-hub project](https://github.com/r-hub)

* The [CRAN task view about package development](https://github.com/ropensci/PackageDevelopment)

* The [`available` package](https://github.com/ropenscilabs/available) to check whether the name you want to use for your package is a) available and b) appropriate.

A very important tip I gave was to have your code read by humans. The automatic tools will flag some things, but the feedback of an human about your code and your documentation will be crucial. And when _you_ review code, you'll learn a lot! If you want to do that, you can volunteer to become a reviewer for rOpenSci, see more info [here](https://github.com/ropensci/onboarding#why-review) and fill the short form [here.](https://ropensci.org/onboarding/)

![](/figure/source/2017-06-17-automatictools/tshirt.jpeg)
After my talk I got a nice RMarkdown t-shirt that has the same colour as my face! Thanks a lot to the organizers, in particular Luca Cerone!
