---
title: "Draw me a project"
date: '2021-06-28'
tags:
  - reproducibility
  - here
  - renv
  - targets
  - orderly
slug: serverside-mathjax
output: hugodown::hugo_document
rmd_hash: 4c54e3036fb02ea4

---

*I'll be giving a keynote talk at the *Rencontres R* (French R conference) in two weeks, all in French.* *This blog post is a written version of my presentation, but in English.* *I decided to not talk about package development for once, but rather about workflows and how to structure & run an analysis.[^1]*

<div class="highlight">

</div>

Why discuss the making of R projects? Because any improvement in your workflow might improve the experience of anyone trying to run or audit an analysis later, including yourself. It's also nice that there are always elements to make better, althought that also means one might be procrastinating instead of making actual progress, so beware!

Now why should *I* discuss the making of R projects? While I am not often in charge of analyses these days, I follow R news quite well so thought I might be able to deliver some useful tips to the audience.

## Some "basics"

To start with I'd like to mention some rules or principles that are quite crucial. My main source of information for them is Jenny Bryan who's a Software Engineer at RStudio.

{{< tweet 1222960316545294337 >}}

Like many I identified with Sharla Gelfand's quote[^2] as reported by Kara Woo on Twitter, "Everything I know is from Jenny Bryan".

{{< figure src="https://raw.githubusercontent.com/MonkmanMH/EIKIFJB/main/EIKIFJB_sigmar_hex.png" alt="hex logo de 'Everything I know is from Jenny Bryan' avec un ordinateur portable en feu" width=350 link="https://github.com/MonkmanMH/EIKIFJB" >}}

There's even a [logo](https://github.com/MonkmanMH/EIKIFJB) created by [Martin Monkman](https://github.com/MonkmanMH) for all of us agreeing with that statement! It features the letters EIKIFJB for "Everything I know is from Jenny Bryan" as well as a laptop on fire.

Why a laptop on fire?!

{{< tweet 940021008764846080 >}}

That's a reference to a talk Jenny Bryan gave years ago, to which she said she'd put your computer of fire if you used `setwd("C:\Users\jenny\path\that\only\I\have")` or `rm(list = ls())` at the beginning of your scripts. This goes against the notion of a ["project-oriented workflow"](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/). I do not need to repeat the wisdom of her blog post in mine, so will only use cliff notes:

-   When reading in data etc. use paths relative to the root of your project, possibly using the [here package by Kirill Müller](https://github.com/jennybc/here_here).
-   Re-start R often, do not e.g. load packages in your [.Rprofile](https://rstats.wtf/r-startup.html#rprofile), [`usethis::use_blank_slate()`](https://usethis.r-lib.org/reference/use_blank_slate.html).

Really, go and read her post if you haven't yet!

In my talk I'll show pictures of the Kei-tora Gardening Contest where participants create beautiful gardens in little trucks, which I find is a good image of a project that should be independent (you can move your truck, you should be able to move your project too).

{{< tweet 1403009688392781824 >}}

As the first part of my talk really is a collection of awesome content by Jenny Bryan, I will also show her rules for [naming files](http://www2.stat.duke.edu/~rcs46/lectures_2015/01-markdown-git/slides/naming-slides/naming-slides.pdf). What Shakespeare said about [roses](https://en.wikipedia.org/wiki/A_rose_by_any_other_name_would_smell_as_sweet) isn't true when programming. 😅

Then I'll "introduce" version control. I'll briefly mention some basic git commands, and most importantly, how to run them! Here are my preferences:

-   Using usethis ([`usethis::use_git()`](https://usethis.r-lib.org/reference/use_git.html), [`usethis::use_github()`](https://usethis.r-lib.org/reference/use_github.html), etc.) and gert ([`gert::git_push()`](https://docs.ropensci.org/gert/reference/git_fetch.html)) so that I don't need to leave R.

-   Using RStudio git pane.

-   Using the terminal for commands copy-pasted from somewhere on the web (and for the ones I do know by heart now!).

-   Using a git interface like GitKraken for more complicated stuff... which I haven't really ever fully explored, actually.

I'll share links to my favorite git resources that I send to anyone who asks (or doesn't ask):

-   [Excuse Me, Do You Have a Moment to Talk About Version Control?](https://www.tandfonline.com/doi/full/10.1080/00031305.2017.1399928), Jenny Bryan.

-   [Happy Git and GitHub for the useR](https://happygitwithr.com/), Jenny Bryan, the STAT 545 TAs, Jim Hester.

{{< tweet 970383613119184896 >}}

-   [Reflections on 4 months of GitHub: my advice to beginners](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/), Suzan Baert.

## How to protect your project from external changes

Here's a [maximum credible accident](https://en.wikipedia.org/wiki/Design-basis_event): you write some pretty and handy code munging your data using `package::my_favorite_function()`. Now you go and update that package and realize `my_favorite_function` is gone! It was apparently removed for good reasons but now your script is broken!

To prevent that, you need to encapsulate your project. You can track and restore package dependencies of your package by using the [renv package](https://rstudio.github.io/renv/) by Kevin Ushey.

Using renv is actually quite easy:

-   In a new project you run `renv::init()`;
-   After that you install and remove packages as you normally would (renv is smart and will copy files from your local not-project library to be faster). Metadata about packagtes (where do they come from) are stored in the `renv.lock` file that you'd put under version control;
-   Anyone getting the project runs `renv::restore()` to have the exact same project library as you.

Now if you want to go further and also freeze the operating system used etc. you could check out [Docker](https://colinfay.me/docker-r-reproducibility/).

## What structure for your project?

[^1]: The Little Prince might have asked "Please draw me a *sheep*", not a *project*, but I liked tweaking that quote for a title as one will often end up putting R projects in boxes (folders).

[^2]: Note that Sharla Gelfand themselves is a great source of good ideas! See e.g. their talk [Don't repeat yourself, talk to yourself! Repeated reporting in the R universe](https://sharla.party/talk/2020-01-01-rstudio-conf/) from the RStudio::conf 2020.

