---
title: "How to become a better R code detective?"
date: '2021-07-12'
tags:
  - package-development
  - debugging
  - reprex
slug: code-detective
output: hugodown::hugo_document
rmd_hash: 0a60a6082f28dc58

---

When trying to fix a bug or add a feature to a package, how do you go from viewing the code as a big messy ball of wool, to a logical diagram that you can bend to your will? In this post, I will share some resources and tips on getting better at debugging and reading code, written by someone else or yourself long enough ago to feel foreign.

From step 2, there's not really an order for the steps, but you definitely want to acquire enough knowledge through research before you tinker, otherwise you will be tinkering quite randomly.

## Step 0: Only deal with well-designed code

This is obviously not an actual tip, but more of a encouragement to try and write "good" code yourself.

<https://style.tidyverse.org/functions.html#comments-1>

<https://style.tidyverse.org/package-files.html#organisation-1>

{{< tweet 1412140590842597385 >}}

It'll also help if the code is well tested: tests can help you understand the goals behind functions, and will help you refactor without breaking features.

## Step 1: Clone and build

In sharing this life-changing tip I am inspired by the talk "Reading other people's code" by Patricia Aas that I actually listened to as [an episode of the All Things Git podcast](https://www.allthingsgit.com/episodes/learning_a_new_codebase_with_patricia_aas.html).

Instead of being overwhelmed by the idea of starting to tinker with a codebase, create a local version-controlled project with the codebase in it! E.g. fork a GitHub repo, and use [`usethis::create_from_github()`](https://usethis.r-lib.org/reference/create_from_github.html). Then install open it, install the dependencies via `remotes::install_deps(dependencies = TRUE)`, build or load it. Before amending things, create a new branch via e.g. `gert::git_branch_create("tinkering")`. I suppose that if I were fancy I'd say this step is your [*mise en place*](https://fortelabs.co/blog/mise-en-place-for-knowledge-workers/).

Obviously to reach that stage you'll need to know *what* codebase is the one to be working on. However, you'll probably start from some code in any case, e.g. your currently buggy code.

## Make your problem smaller

-   reprex.
-   Clear scope of what you're after.

## Pull an end / Follow the trails

As you are not going to read code from cover to cover, you'll need to find a logical way to explore the code.

I like the phrase *follow the trails* by Kara Woo in her excellent RStudio conference talk ["Box plots A case study in debugging and perseverance"](https://www.rstudio.com/resources/rstudioconf-2019/box-plots-a-case-study-in-debugging-and-perseverance/) as well as the phrase *pull an end* by Patricia Aas in her also excellent talk ["Reading Other People's Code"](https://patricia.no/2018/09/19/reading_other_peoples_code.html)

### Find where to start

Easy case: there's a message on screen telling you where an error occurred, or you know what function you want to amend. Alternatively,

-   You can put the error / warning in a search engine.
-   If there's an unclear error you can try to see the traceback i.e. what functions were called leading to that error. In her talk ["Object of type 'closure' is not subsettable"](https://github.com/jennybc/debugging#readme), Jenny Bryan explains very well what a traceback is. In my `.Rprofile` I have

``` r
options(
error = rlang::entrace, 
rlang_backtrace_on_error = "branch")
```

thanks to a [tweet by Noam Ross](https://twitter.com/noamross/status/1202269314029621251) reporting a tip by Jenny Bryan. "It gives trimmed tracebacks when using pipes."

-   If there's no error but say a warning you could try to [convert the warning to an error](https://adv-r.hadley.nz/debugging.html#non-error-failures).

### Explore from that starting point

-   You can use "grepping" as said by Patricia Aas: look for the occurrences of a function or variable name in a local folder, or via GitHub (advanced) search. You can limit the hits to some types of files e.g. R scripts in `R/`.
-   In your IDE e.g. RStudio there might be a way to go directly to the *definition* of function.

### How to read code

-   Hopefully the code makes sense on its own.
-   Sometimes using git blame or looking at the git history might help understand the context of some aspects of the code, if there's no code comment referring an issue. Do not actually *blame* people, though.

## Build your mental model of the code

That's what Patricia Aas call "mental machine". You might want to draw some sort of diagram by hand (or programmatically).

## Beyond browsing files, `browser()`

Resources for learning proper debugging tools.

## Read tests? Write some for sure

## Rubberducking to a persona

{{< tweet 1409533060790558725 >}}

## Asking for help

## Reading other people's debugging journeys, document yours

Sadly people will often only take the time to document their debugging journey when the bug is especially tricly or weird. Besides, few people write actual debugging games.

In the meantime, you might enjoy watching or hearing some debugging journeys. You will notice how these programmers make and invalidate hypotheses.

## Conclusion

