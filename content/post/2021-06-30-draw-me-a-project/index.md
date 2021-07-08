---
title: "Draw me a project"
date: '2021-06-30'
tags:
  - reproducibility
  - here
  - renv
  - targets
  - orderly
slug: r-projects
output: hugodown::hugo_document
rmd_hash: 860e218718e80a2e

---

*I'll be giving a remote keynote talk at the [*Rencontres R*](https://rr2021.sciencesconf.org/program) (French R conference) on July the 12th, all in French.* *This blog post is a written version of my presentation, but in English.* *I decided to not [talk](/talks/) about package development for once, but rather about workflows and how to structure & run an analysis.[^1]*

*Many thanks to [Christophe Dervieux](https://cderv.rbind.io/) for useful feedback on this post! Merci beaucoup !*

*This post was featured on the [R Weekly podcast](https://share.fireside.fm/episode/87RSVeFz+W6_0jLXR) by Eric Nantz.*

<div class="highlight">

</div>

Why discuss the making of R projects? Because any improvement in your workflow might improve the experience of anyone trying to run or audit an analysis later, including yourself. It's also nice that there are always elements to refine, although that also means one might be procrastinating instead of making actual progress, so beware!

Now why should *I* discuss the making of R projects? While I am not often in charge of analyses these days, I [follow R news](/2019/01/25/uptodate/) quite well so thought I might be able to deliver some useful tips to the audience.

## Some "basics"

To start with I'd like to mention some rules or principles that are quite crucial. My main source of information for them is [Jenny Bryan](https://jennybryan.org/).

{{< tweet 1222960316545294337 >}}

Like many I identified with Sharla Gelfand's quote[^2] as reported by Kara Woo on Twitter, "Everything I know is from Jenny Bryan".

{{< figure src="https://raw.githubusercontent.com/MonkmanMH/EIKIFJB/main/EIKIFJB_sigmar_hex.png" alt="hex logo de 'Everything I know is from Jenny Bryan' avec un ordinateur portable en feu" width=350 link="https://github.com/MonkmanMH/EIKIFJB" >}}

There's even a [logo](https://github.com/MonkmanMH/EIKIFJB) created by [Martin Monkman](https://github.com/MonkmanMH) for all of us agreeing with that statement! It features the letters EIKIFJB for "Everything I know is from Jenny Bryan" as well as a laptop on fire.

Why a laptop on fire?!

{{< tweet 940021008764846080 >}}

That's a reference to a talk Jenny Bryan gave years ago, to which she said she'd put your computer on fire if you used `setwd("C:\Users\jenny\path\that\only\I\have")` or `rm(list = ls())` at the beginning of your scripts. This goes against the notion of a ["project-oriented workflow"](https://www.tidyverse.org/blog/2017/12/workflow-vs-script/). I do not need to repeat the wisdom of her blog post in mine, so will only use cliff notes & some remarks:

-   It's handy to create your project with [`usethis::create_project()`](https://usethis.r-lib.org/reference/create_package.html). RStudio IDE and projects work well together cf [IDE support for projects](https://rstats.wtf/project-oriented-workflow.html#ide-support-for-projects) and [RStudio projects](https://rstats.wtf/project-oriented-workflow.html#rstudio-projects) (although you could use the principles of a project-oriented workflow outside of RStudio!).

-   When reading in data etc. use paths relative to the root of your project, possibly using the [here package by Kirill Müller](https://here.r-lib.org/articles/here.html).

-   Re-start R often, do not e.g. load packages in your [.Rprofile](https://rstats.wtf/r-startup.html#rprofile), [`usethis::use_blank_slate()`](https://usethis.r-lib.org/reference/use_blank_slate.html).

Really, go and read her post if you haven't yet!

In my talk I'll show pictures of the Kei-tora Gardening Contest where participants create beautiful gardens in little trucks, which I find is a good image of a project that should be independent (you can move your truck, you should be able to move your project too).

{{< tweet 1403009688392781824 >}}

As the first part of my talk really is a collection of awesome content by Jenny Bryan, I will also show her rules for [naming files](http://www2.stat.duke.edu/~rcs46/lectures_2015/01-markdown-git/slides/naming-slides/naming-slides.pdf). What Shakespeare said about [roses](https://en.wikipedia.org/wiki/A_rose_by_any_other_name_would_smell_as_sweet) isn't true when programming. 😅

Then I'll "introduce" version control. I'll first underline that you want to have a back-up of your stuff somewhere no matter how you do it (so even if you do not use git, think of backing things up with a good system!). I'll briefly mention some basic git commands, and most importantly, how to run them! Here are my preferences:

-   Using usethis ([`usethis::use_git()`](https://usethis.r-lib.org/reference/use_git.html), [`usethis::use_github()`](https://usethis.r-lib.org/reference/use_github.html), etc.) and gert ([`gert::git_push()`](https://docs.ropensci.org/gert/reference/git_fetch.html)) so that I don't need to leave R.

-   Using RStudio git pane to not be too far from the R console.

-   Using the terminal for commands copy-pasted from somewhere on the web (and for the ones I now know by heart!).

-   Using a git interface like GitKraken (or the editor VSCODE that has a good git integration) for more complicated stuff... which I haven't really ever fully explored, actually.

I'll share links to my favorite git resources that I send to anyone who asks (or doesn't ask):

-   [Excuse Me, Do You Have a Moment to Talk About Version Control?](https://www.tandfonline.com/doi/full/10.1080/00031305.2017.1399928), Jenny Bryan.

-   [Happy Git and GitHub for the useR](https://happygitwithr.com/), Jenny Bryan, the STAT 545 TAs, Jim Hester.

{{< tweet 970383613119184896 >}}

-   [Reflections on 4 months of GitHub: my advice to beginners](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/), Suzan Baert.

And in French there's a great blog post by ThinkR: [Travailler avec Git via RStudio et versionner son code](https://thinkr.fr/travailler-avec-git-via-rstudio-et-versionner-son-code/).

## How to protect your project from external changes

Here's a [maximum credible accident](https://en.wikipedia.org/wiki/Design-basis_event): you write some pretty and handy code munging your data using `package::my_favorite_function()`. Now you go and update that package and realize `my_favorite_function` is gone! It was apparently removed for good reasons but now your script is broken!

To prevent that, you need to encapsulate your project. You can track and restore package dependencies of your package by using the [renv package](https://rstudio.github.io/renv/) by Kevin Ushey. The renv package is the successor of the packrat package by the same author.

Using renv is actually quite easy:

-   In a new project you run [`renv::init()`](https://rstudio.github.io/renv//reference/init.html);
-   After that you install and remove packages as you normally would (renv is smart and will copy files from your local not-project library to be faster). Regularly run [`renv::snapshot()`](https://rstudio.github.io/renv//reference/snapshot.html).[^3] Metadata about packages (where do they come from) are stored in the `renv.lock` file that you'd put under version control;
-   Anyone getting the project runs [`renv::restore()`](https://rstudio.github.io/renv//reference/restore.html) to have the exact same project library as you.

Also worth of a mention is [capsule](https://github.com/MilesMcBain/capsule) by Miles McBain that is "an inversion of renv for low effort reproducible R package libraries".

Now if you want to go further and also freeze the operating system used etc. you could check out [Docker](https://colinfay.me/docker-r-reproducibility/).

## What structure for your project?

What's in your project? Probably something like:

-   Data or the code to get them from a database or a remote resource;
-   Some code munging and analysing them;
-   Some output that could be a graph, a report etc.

Now how should you structure your project? It's important to use a structure that's consistent between your (team's) project, and that can be created automatically.

While I have never used the [ProjectTemplate package](https://github.com/KentonWhite/ProjectTemplate) by Kenton White, I really like the blog post [Love for ProjectTemplate](https://hilaryparker.com/2012/08/25/love-for-projecttemplate/) by Hilary Parker as it underlines advantages that should be requirement for any tool that helps create an analysis.

1.  *"Routine is your friend".*
2.  *"It's easier to start somewhere and then customize, rather than start from the ground up."*
3.  *"Reproducibility should be as easy as possible."*
4.  *"Finding things should also be as easy as possible."*

Now some people find all these advantages by structuring their analyses as R packages. Creating an R package to share code and data you use throughout projects is not subject to debate: it's great! You can even build [Shiny apps as package with golem](https://golemverse.org/). Creating your analysis as a package, with dependencies in `DESCRIPTION`, functions in `R/`, analysis in e.g. a vignette, *is* subject to debate.

The advantages are that when doing that you can re-use or refresh your package development skills, and foremost that you can re-use tools made for package development (like devtools and usethis). There's a paper presenting and promoting the approach, where such packages are called research compendia: [Packaging Data Analytical Work Reproducibly Using R (and Friends)](https://www.tandfonline.com/doi/abs/10.1080/00031305.2017.1375986?journalCode=utas20), Ben Marwick, Carl Boettiger & Lincoln Mullen (2018), The American Statistician, 72:1, 80-88, DOI: \<10.1080/00031305.2017.1375986\>

There are specific tools for building and using research compendia:

-   [rrtools](https://github.com/benmarwick/rrtools) by Ben Marwick. *"The goal of rrtools is to provide instructions, templates, and functions for making a basic compendium suitable for writing a reproducible journal article or report with R."*
-   [holepunch](https://karthik.github.io/holepunch/) by Karthik Ram. *"holepunch will read the contents of your R project on GitHub, create a DESCRIPTION file with all dependencies, write a Dockerfile, add a badge to your README, and build a Docker image. Once these 4 steps are complete, any reader can click the badge and within minutes, be dropped into a free, live, RStudio server. Here they can run your scripts and notebooks and see how everything works."* (🤫 holepunch works [without the compendium structure](https://github.com/karthik/holepunch#alternate-setup-method) as well.)

Now it's good to know not everyone loves the idea of projects as R packages. Miles McBain wrote a blog post ["Project as an R package: An okay idea"](https://www.milesmcbain.com/posts/an-okay-idea/).

I found this quote quite interesting:

> *"My response to advocates of project as a package is: ==You're wasting precious time making the wrong packages.=="*

> *"Instead of shoehorning your work into the package development domain, with all the loss of fidelity that entails, why aren't you packaging tools that create the smooth {devtools}/{usethis} style experience for your own domain?"*

In my talk my own advice is to use whatever structure you, and your team if you have one, prefers, and to choose a structure that can be created automatically. You could be the one creating the package to create projects, as Miles said -- although he also mentioned the [risk of "bitrot"](https://www.milesmcbain.com/posts/an-okay-idea/#where-to-next) for tools maintained and used by few people.

## How to run your project?

How do you go from resources and scripts to the analysis output? If your project "only" contains one or a few R Markdown document(s), maybe you can simply use the knit button. Now you might be dealing with some challenges warranting the use of dedicated tools. I'll briefly present two.

*Note that not all tools separate ways to structure and run projects i.e. you could be using a workflow package that's opinionated about both.*

### Optimize pipeline with targets

The [targets package](https://books.ropensci.org/targets/walkthrough.html#inspect-the-pipeline) by Will Landau, reviewed at rOpenSci software peer-review, helps optimizing pipelines by recognizing dependencies between steps (e.g. if you change the raw data you need to re-run everything, but if you change only the model fit you only need to re-run the final plot) and only running those that are needed at the moment. To make a project a targets project you need a script called `_targets.R` where you load packages, source e.g. functions from `R/` and define *targets*. Now what are targets? Looking at part of a `_targets.R` from targets manual,

``` r
list(
  # Raw data file. Notice the format argument is used.
  tar_target(
    raw_data_file,
    "data/raw_data.csv",
    format = "file"
  ),
  tar_target(
    raw_data,
    read_csv(raw_data_file, col_types = cols())
  ),
  # The dplyr package has been previously loaded
  tar_target(
    data,
    raw_data %>%
      filter(!is.na(Ozone))
  ),
  # The create_plot() function comes from a script that has been previously sourced
  tar_target(hist, create_plot(data)),
  tar_target(fit, biglm(Ozone ~ Wind + Temp, data))
)
```

the targets are defined in a list. Each of them uses the `tar_target()` function, has a name, and code that creates it, or, in the case of the raw data file, the path to the file. To build the project you run [`targets::tar_make()`](https://docs.ropensci.org/targets/reference/tar_make.html) (and to destroy everything if you need to, [`targets::tar_destroy()`](https://docs.ropensci.org/targets/reference/tar_destroy.html)). To see the network of dependencies between targets you can run [`targets::tar_glimpse()`](https://docs.ropensci.org/targets/reference/tar_glimpse.html) and other functions that [inspect the pipeline](https://books.ropensci.org/targets/walkthrough.html#inspect-the-pipeline).

There's a [whole ecosystem of packages around targets, the Targetopia](https://wlandau.github.io/targetopia/) e.g. the tarchetypes package defines targets making functions that lets you define targets that need to be re-run after a certain time.

To get started with targets, I'd recommend:

-   Reading the [manual](https://books.ropensci.org/targets/);

-   Watching the talk [Reproducible Computation at Scale in R with {targets}](https://www.youtube.com/watch?v=FODSavXGjYg) (Will Landau at the RUG of Lille).

-   Starting with a small project (my current non-expertise level 😅).

To follow evolutions of targets as it keeps getting better, you can:

-   [Release-watch](https://github.blog/changelog/2018-11-27-watch-releases/) the [targets](https://github.com/ropensci/targets) GitHub repo;

-   Follow [Will Landau](https://twitter.com/wmlandau) on Twitter;

-   Subscribe to [rOpenSci newsletter](https://ropensci.org/news/).

## Track versions of an analysis with orderly

Imagine you want to keep track of the different versions of an analysis and everything that went into it, and to run analysis comparing versions. The [orderly package](https://www.vaccineimpact.org/orderly/), maintained by Rich FitzJohn[^4], offers an infrastructure for that kind of workflows.

With orderly you have repos and in repos you have reports/tasks, or only one report/task. Here's an example with an orderly repo with one report. From an RStudio project I ran `orderly::orderly_init("blop")` which created the repo in a new folder "blop", and then `orderly::orderly_new("example", "blop")` after which I modified files using [the orderly introduction vignette](https://www.vaccineimpact.org/orderly/articles/orderly.html).

In the `blop/` folder there's a general orderly configuration that I haven't needed to touch, `orderly_config.yml`. There's also a `src/` folder corresponding to the source of my example report.

    blop
    ├── orderly_config.yml
    └── src
        └── example
            ├── orderly.yml
            └── script.R

That example report consists of two files so it's a very small one. You could put anything in there as orderly is not opinionated about that! It *is* opinionated about being told about packages, resources, scripts, artefacts in the `blop/src/example/orderly.yml` configuration.

In the example the configuration lists one script and two artefacts:

``` yaml
script: script.R

artefacts:
  - staticgraph:
      description: A graph of things
      filenames: mygraph.png
  - data:
      description: Data that went into the plot
      filenames: mydata.csv
```

The script creates those two artefacts:

``` r
dat <- data.frame(x = 1:10, y = runif(10))
write.csv(dat, "mydata.csv", row.names = FALSE)

png("mygraph.png")
plot(dat)
dev.off()
```

Now how do you run the project?

1.  You can use the [development mode](https://www.vaccineimpact.org/orderly/articles/orderly.html#developing-a-report-1) when developing a report, to have things at your current working directory.
2.  You can build a draft version of the report with `id <- orderly::orderly_run("example", root = "blop")` after which your whole analysis, input and output, appears in `blop/draft/example/some-id-that-contains-the-date-and-a-hash`.
3.  If you like it you can commit it with `orderly::orderly_commit(id, root = "blop")` which moves the whole analysis folder from `blop/draft/example/some-id-that-contains-the-date-and-a-hash` to `blop/archive/example/some-id-that-contains-the-date-and-a-hash`.

Note that as the draft and archive folders can be gigantic you are expected to back up with some system. You are expected *not* to use git to track these folders as git does not behave well with very large files. You can still use git to track other files in the orderly repo.

To get started with orderly you can read the [orderly website](https://www.vaccineimpact.org/orderly/) that I found very clear, and you should start with a small project... which again is my current non-expertise level.

To follow orderly news, as it's actively used and developed, you can:

-   [Release-watch](https://github.blog/changelog/2018-11-27-watch-releases/) the [orderly GitHub repo](https://github.com/vimc/orderly);

-   read [the blog of the team behind orderly](https://reside-ic.github.io/);

-   follow [Rich FitzJohn](https://twitter.com/rgfitzjohn) on Twitter.

### Other tools for building analyses

The [targets](https://docs.ropensci.org/targets) and [orderly](https://www.vaccineimpact.org/orderly) packages are not the only ones helping run analyses: [workflowr](https://jdblischak.github.io/workflowr/), [cabinets](https://github.com/nt-williams/cabinets), [etc.](https://cran.r-project.org/web/views/ReproducibleResearch.html). You might even want to build your own!

## Conclusion

In this post/talk I have discussed several aspects of drawing a project

-   Some "basics" that are not all easy (all are habits to take, and some are trickier to learn than others);
-   Encapsulating your project by tracking its dependencies with e.g. renv;
-   Structuring your project in a way that suits your team's wishes, is consistent over time, and can be automated;
-   Using tools for building outputs that answers your needs (optimizing a pipeline? tracking versions of an analysis projects?).

All in all my tips would be to read everything Jenny Bryan writes :grin:, and to not be afraid to change tools over time as new cool tools will appear and as your needs and experience will change. I'd be interested to hear any thoughts in the comments below!

### Further resources

These items might be relevant for you:

-   [Course "Reproducible Research Data and Project Management in R"](https://annakrystalli.me/rrresearchACCE20/) by Anna Krystalli.

-   [Guide "Organisation d'un projet collaboratif de publication PROPRE"](https://rdes_dreal.gitlab.io/publication_guide/production/index.html#quest-ce-quun-projet-collaboratif-propre) de Sébastien Rochette.

-   [Good enough practices in scientific computing](https://journals.plos.org/ploscompbiol/article?id=10.1371/journal.pcbi.1005510) Wilson G, Bryan J, Cranston K, Kitzes J, Nederbragt L, et al. (2017) Good enough practices in scientific computing. PLOS Computational Biology 13(6): e1005510. <https://doi.org/10.1371/journal.pcbi.1005510>

-   [The Turing Way](https://the-turing-way.netlify.app/welcome), an open source community-driven guide to reproducible, ethical, inclusive and collaborative data science.

[^1]: The Little Prince might have asked "Please draw me a *sheep*", not a *project*, but I liked tweaking that quote for a title as one will often end up putting R projects in boxes (folders, *maybe* packages).

[^2]: Note that Sharla Gelfand themselves is a great source of good ideas! See e.g. their talk [Don't repeat yourself, talk to yourself! Repeated reporting in the R universe](https://sharla.party/talk/2020-01-01-rstudio-conf/) from the RStudio::conf 2020.

[^3]: Unless you go for [automatic snapshots](https://rstudio.github.io/renv/reference/config.html#configuration).

[^4]: Many thanks to Rich for answering some questions of mine!

