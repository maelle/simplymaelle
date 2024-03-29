---
title: "Two recent enhancements to my testing workflow"
date: '2023-10-09'
slug: test-workflow-enhancement
output: hugodown::hugo_document
rmd_hash: f815a3625776be16
tags:
  - good practice
  - code style
  - package development
  - testing
---

I spend a lot of quality time with testthat, that sometimes deigns to praise my code with emojis, sometimes has to encourage me. No one gets it right on their first try apparently?

Anyway, in honor of [testthat 3.2.0 release](https://www.tidyverse.org/blog/2023/10/testthat-3-2-0/) :tada: :clap:, I'd like to mention two small things that improved my testing workflow a whole lot!

## Running one single test at a time

Under [testthat 3.2.0 minor features](https://testthat.r-lib.org/news/index.html#minor-features-and-bug-fixes-3-2-0) lies a small gem:

> `test_file()` gains a desc argument which allows you to run a single test from a file. [#1776](https://github.com/r-lib/testthat/issues/1776)

This is huge! When I am struggling with a single test called "My great function works", I can now call `devtools::test_active_file(desc = "My great function works")`! I'm not entirely sure yet it perfectly integrates with snapshot recording, but I'm very happy nonetheless.

Now if I were struggling with more tests at a time (Remember, no one gets it right on their first try :sweat_smile:), I should use the handy [lazytest](https://github.com/cynkra/lazytest) package.

## Getting back to the package directory and project

**Update on 2023-10-16: here's a [better, more general solution to fix local mess from a test](/2023/10/16/test-local-mess-reset/).**

I recently realized that if...

-   you develop a package using RStudio IDE (which I do!);

-   your package tests change the current directory / the usethis project (for [reasons](https://github.com/cynkra/fledge) 😅 ) for instance with [`withr::local_dir()`](https://withr.r-lib.org/reference/with_dir.html)/[`usethis::local_project()`](https://usethis.r-lib.org/reference/proj_utils.html);

-   you run test code interactively & thus lose your current directory/project...

the wonderful `studioapi::getActiveProject()` function remembers where the actual project, that is your package, is, very much like clicking on the RStudio hex logo in the Files tab.

Based on that knowledge I added a helper function to the tests of a package I work on, to have it at hand after running [`devtools::load_all()`](https://devtools.r-lib.org/reference/load_all.html):

``` r
back_to_start <- function() {
  current_dev_project <-  rstudioapi::getActiveProject()
  setwd(current_dev_project)
  usethis::proj_set(current_dev_project)
}
```

So I can make a mess in my console and then run `back_to_start()` to get back where I started.

## Conclusion

In this short post I presented two recent enhancements to my testing workflow: the adoption of the new `desc` argument of [`testthat::test_file()`](https://testthat.r-lib.org/reference/test_file.html) to run one single test at a time; and the "discovery" of a handy way to find my way back to the package directory after experimenting in temporary toy directories.

