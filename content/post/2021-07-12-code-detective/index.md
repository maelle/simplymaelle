---
title: "How to become a better R code detective?"
date: '2021-07-12'
tags:
  - package-development
  - debugging
  - reprex
slug: code-detective
output: hugodown::hugo_document
rmd_hash: 44fa56233014f4d3

---

*Huge thanks to [Hannah Frick](https://www.frick.ws/) for her useful feedback on this post! Vielen Dank!*

When trying to fix a bug or add a feature to an R package, how do you go from viewing the code as a big messy ball of wool, to a logical diagram that you can bend to your will? In this post, I will share some resources and tips on getting better at debugging and reading code, written by someone else (or yourself but long enough ago to feel foreign!).

After step 1, there's not really an order for applying the techniques, but you definitely want to acquire enough knowledge through research before you tinker, otherwise you will be tinkering quite randomly.

Apart from the idea of adding tests, most tips could apply to non-package code. If your motivation is to contribute to R itself please refer to [A Guide to Contributing to R Core](https://forwards.github.io/rdevguide/introduction.html) by Saranjeet Kaur and colleagues.

## Step 0: Only deal with well-designed code

This is obviously not an actual tip, but more of a encouragement to try and write better and better code ourselves.

The [Tidyverse style guide](https://style.tidyverse.org/) is a good read, both for learning tips, and for seeing what aspects such a style guide applies to. Of particular interest are e.g. the mention of [function comments](https://style.tidyverse.org/functions.html#comments-1), and [file organization](https://style.tidyverse.org/package-files.html#organisation-1).

Regarding code comments, I was intrigued and impressed by the notion of "explaining variables" mentioned in the tweet below referring a [great short post](https://blog.thepete.net/blog/2021/06/24/explaining-variable/).

{{< tweet 1412140590842597385 >}}

Beyond its being well designed, it'll also help if the code is well tested: tests can help you understand the goals behind functions, and will help you re-factor without breaking features.

Now, we don't always choose what code we get, and even if it was well-designed, we might still need detective skills anyway!

## Step 1: Clone and build

In sharing this life-changing tip I am merely repeating the talk "Reading other people's code" by Patricia Aas that I actually listened to as [an episode of the All Things Git podcast](https://www.allthingsgit.com/episodes/learning_a_new_codebase_with_patricia_aas.html). The techniques presented by Patricia Aas are not specific to R but many of them are relevant for R codebases.

Now to this tip... Instead of being overwhelmed by the idea of starting to tinker with a codebase, create a local version-controlled project with the codebase in it! E.g. fork a GitHub repo, and use [`usethis::create_from_github()`](https://usethis.r-lib.org/reference/create_from_github.html). Then open it, install the dependencies via `remotes::install_deps(dependencies = TRUE)`, build or load it. Before amending things, create a new branch via e.g. `gert::git_branch_create("tinkering")`. I suppose that if I were fancy I'd say this step is your [*mise en place*](https://fortelabs.co/blog/mise-en-place-for-knowledge-workers/).

Obviously to reach that stage you'll need to know *what* codebase is the one to be working on. However, you'll probably start from some code in any case, e.g. your currently buggy code.

## Make your problem smaller

In case of a bug, you'll often be advised to make it a minimal reproducible example. While you'll often hear this when you try to communicate your bug to someone else, it is also great practice to do this for yourself! An important thing to know here is *reprex*. A reprex is both a concept (reprex for reproducible example) and a package for communicating such examples, respectively promoted and maintained by Jenny Bryan. Why use reprex?

-   The isolated bug is easier to solve or will be solved by creating it! In Jenny Bryan's talk ["Object of type 'closure' is not subsettable"](https://github.com/jennybc/debugging#readme) there's an example of original code and its minified version.

-   As it is run in an isolated session you can be more sure that it's reproducible.

-   You can send your bug in a format ideal for experts! But you might be writing a reprex just for yourself, to accompany some notes in a GitHub issue for instance.

How does reprex work?

-   You write some code in an editor (including loading libraries, creating toy data etc.).

-   You copy the code to your clipboard.

-   You run [`reprex::reprex()`](https://reprex.tidyverse.org/reference/reprex.html) and reprex runs your code in an isolated session!

-   You get the rendered code on the clipboard (and a preview in RStudio Viewer pane)! Error messages rendered, images uploaded to imgur.

-   You paste the rendered code somewhere, potentially to show to someone.

To learn more about reprex and adopt it, I'd recommend watching [the RStudio webinar about reprex](https://resources.rstudio.com/webinars/help-me-help-you-creating-reproducible-examples-jenny-bryan) and reading reprex vignettes, in particular ["Reprex do's and don'ts"](https://reprex.tidyverse.org/articles/reprex-dos-and-donts.html).

Also in the case of a bug, maybe you don't need to read this post further if your problem is in the bingo below. Often, you'll only notice "obvious" mistakes after making the problem smaller (or after taking a break!).

{{< tweet 1354508785365078016 >}}

In case of amending the features of a package, it'll be important to clearly defined the scope of what you're after. Easier for your work as a code detective, but also for many other reasons, see [Sarah Drasner's post *How to Scope Down PRs*](https://www.netlify.com/blog/2020/03/31/how-to-scope-down-prs/) and Yihui Xie's post [*"Bite-sized" Pull Requests*](https://yihui.org/en/2018/02/bite-sized-pull-requests/).

## Pull an end / Follow the trails

As you are not going to read code from cover to cover, you'll need to find a logical way to explore the code.

I like the phrase *follow the trails* by Kara Woo in her excellent RStudio conference talk ["Box plots - A case study in debugging and perseverance"](https://www.rstudio.com/resources/rstudioconf-2019/box-plots-a-case-study-in-debugging-and-perseverance/) as well as the phrase *pull an end* by Patricia Aas in her also excellent talk ["Reading Other People's Code"](https://patricia.no/2018/09/19/reading_other_peoples_code.html) already mentioned in this post.

### Find where to start

Easy case: there's a message on screen telling you where an error occurred, or you know what function you want to amend.

Alternatively,

-   You can put the error / warning in a search engine.
-   If there's an unclear error you can try to see the traceback i.e. what functions were called leading to that error. In her talk ["Object of type 'closure' is not subsettable"](https://github.com/jennybc/debugging#readme), Jenny Bryan explains very well what a traceback is. In my `.Rprofile` I have

``` r
options(
error = rlang::entrace, 
rlang_backtrace_on_error = "branch")
```

thanks to a [tweet by Noam Ross](https://twitter.com/noamross/status/1202269314029621251) reporting a tip by Jenny Bryan. "It gives trimmed tracebacks when using pipes."

-   If there's no error but a warning you could try to [convert the warning to an error](https://adv-r.hadley.nz/debugging.html#non-error-failures).

### Explore from that starting point

-   You can use "grepping" as said by Patricia Aas: look for the occurrences of a function or variable name in a local folder, or via [GitHub (advanced) search](https://docs.github.com/en/github/searching-for-information-on-github/getting-started-with-searching-on-github/about-searching-on-github). You can limit the hits to some types of files e.g. R scripts in `R/`.
-   In your IDE e.g. RStudio there might be a way to go directly to the *definition* of function. With the [lookup package](https://github.com/jimhester/lookup) you can also easily look up the source of functions [locally and on GitHub](https://blog.r-hub.io/2019/05/14/read-the-source/).

### How to read code: space... and time

-   Hopefully the code makes sense on its own. If not, fear not, the next item right here, and the other sections of this post, as well as patience, will help.
-   Sometimes [using git blame or looking at the git history](https://docs.github.com/en/github/managing-files-in-a-repository/managing-files-on-github/tracking-changes-in-a-file) might help understand the context of some aspects of the code, if there's no code comment referring an issue. Do not actually *blame* people, though. To make your own git history more informative for such later code archaeology, use branches and squash and merge.

## Build your mental model of the code

That's what [Patricia Aas calls "mental machine"](https://www.allthingsgit.com/episodes/learning_a_new_codebase_with_patricia_aas.html). You might want to draw some sort of diagram by hand (or [programmatically](https://blog.r-hub.io/2019/12/12/internal-functions/#explore-internal-functions-within-a-package)). Patricia Aas remarks that such diagrams might even be contributed to the codebase as developer documentation.

## Browse code by others

The life-hack below by Julia Silge for fixing Travis CI builds[^1], looking at other people's configuration files, applies to any code endeavour.

{{< tweet 1205183124868681728 >}}

How to find good examples?

-   The lookup package can help you [look up usage of a function on GitHub](https://blog.r-hub.io/2019/05/14/read-the-source/#how-to-search-the-source) and in general [GitHub Advanced search](https://github.com/search/advanced) is really useful;
-   You might look at the reverse dependencies of a package you are using;
-   You might try to think of packages doing something similar to yours (e.g. another package munging XML data from an API ; another package wrapping a C library).

## Beyond browsing files, `browser()`

Reading code and imagining what it does only goes so long. You can edit the code and see whether, from the outside, it does what you want it to. Sometimes you might also make do with print-debugging i.e. for instance writing `print("coucou !")` to check a part of the code was run, or `print(class(x))` to check an assumption about a thing. Sometimes print-debugging is the only technique you might be able to use if [non-interactive debugging](https://adv-r.hadley.nz/debugging.html#print-debugging). It can also be perfect to know where a loop breaks which motivated the tweet below by Sharla Gelfand:

{{< tweet 1382090144229044226 >}}

But often you will have to go experiment under the hood. For doing that efficiently you will need to learn about [`browser()`](https://rdrr.io/r/base/browser.html) and friends! Or only just [`browser()`](https://rdrr.io/r/base/browser.html) to start with!

The basic idea is that you just replace the [`print()`](https://rdrr.io/r/base/print.html) command you were about to write with [`browser()`](https://rdrr.io/r/base/browser.html), run the code and voilà! You entered the debugger and can run code line by line, explore options and environment variables, etc. Over time it'll become a habit of yours, at least that's what happened to me once I saw the light. 😁

Here are some good resources to learn about debugging tools. These resources also overlap with some of the objectives of this very blog post.

-   [Debugging advice in the Advanced R book by Hadley Wickham](https://adv-r.hadley.nz/debugging.html);

-   [Webinar debugging techniques in RStudio by Amanda Gadrow](https://www.rstudio.com/resources/webinars/debugging-techniques-in-rstudio/);

-   [Debugging advice in the Hands-on programming with R book by Garrett Grolemund](https://rstudio-education.github.io/hopr/debug.html);

-   [Debugging advice in the materials of the course "What they forgot to teach you about R" by Jenny Bryan and Jim Hester](https://rstats.wtf/debugging-r-code.html) -- it even includes tips for R Markdown debugging;

-   [Jenny Bryan's talk "Object of type 'closure' is not subsettable"](https://github.com/jennybc/debugging#readme).

### Beyond R

Sometimes the bug or element to tweak will live outside of R. Maybe in some C code you are wrapping, maybe in a CSS file. You will therefore have to learn debugging tools for these things too!

## Read tests? Write some for sure

In Patricia Aas' [techniques](https://patricia.no/2018/09/19/reading_other_peoples_code.html) features the idea of writing and running tests to see what's the code is supposed to do. She especially mentions integration tests, whereas in R packages you'll mostly find unit tests. Those can also be useful to read, especially when they start breaking after your experiments.

In any case, once you have amended a codebase to fix a bug or add a feature, add tests! In Kara Woo's talk ["Box plots - A case study in debugging and perseverance"](https://www.rstudio.com/resources/rstudioconf-2019/box-plots-a-case-study-in-debugging-and-perseverance/), she explained she added tests. In [Jenny Bryan's talk "Object of type 'closure' is not subsettable"](https://github.com/jennybc/debugging#readme) she uses the word "deter" in the [part of the talk where she gives such advice](https://speakerdeck.com/jennybc/object-of-type-closure-is-not-subsettable?slide=69): adding tests and assertions, but also other tips such as running those on continuous integration, "using mind bendy stuff in moderation", leaving access panels (e.g. verbose modes), writing error messages for humans.

You could even write a failing test at the beginning of your code exploration, [even leaving it failing for an easier restart when you come back to the codebase](https://r-pkgs.org/tests.html) (better than a sticky note for sure!).

## Rubberducking to a persona

Another technique you will often see mentioned is rubberducking i.e. explaining your problem to a rubber duck. The simple act of phrasing your issue might help you solve it.

However, you might prefer to speak to an actual person, or pretend you are as written in the tweet by Julia Evans below:

{{< tweet 1409533060790558725 >}}

I liked seeing that as I sometimes open a Slack conversation with someone as if I were about to ask for their help, and whilst preparing my notes for them, I'll solve my issue.

## Refactoring

Another [tip by Patricia Aas](https://patricia.no/2018/09/19/reading_other_peoples_code.html) is refactoring the code as it might improve your understanding of it. They underline that you should not contribute the results of your refactoring, especially as a first PR, as people might hate you! It's an exercise for you.

That said, I remember receiving nice PRs from someone who had just read the [Clean Code book by Robert C. Martin](https://www.goodreads.com/book/show/3735293-clean-code) . They started with a small one, and were very polite. Since then I've seen [some bad critic of the book](https://qntm.org/clean) but these PRs made perfect sense. I can however easily imagine a big refactoring PR would not be happily received!

So, in a nutshell, as said by Patricia Aas, refactor to learn, *in your own local copy or your own fork*.

## Asking for help

As nice as solving a problem on one's own is, asking for help might be the solution! It is also a skill, or more, a bunch of skills: both how and where to ask for help but also deciding when to ask for help, when it's no longer worth anyone's money to have you continuing to work alone on a problem.

### How to ask for help?

In the section of this post about making your problem smaller, we mentioned creating a reprex and having a clear scope. These are elements that will be featured in your plea for help.

It can also be good if you have [tracked your progress](https://wizardzines.com/comics/track-your-progress/), the various things you have tried.

### Where to ask for help?

Where to ask for help depends on your question and the codebase you are working on. If you are working on a pull request in a package where you already have a good working relationship with other developers, or where you were encouraged to open a PR (directly or via the contributing guide), you might get help from other contributors.

I wrote a blog post on [where to get help with your R questions](/2018/07/22/wheretogethelp/).

See also my post on the R-hub blog, [*How to get help with R package development? R-package-devel and beyond*](https://blog.r-hub.io/2019/04/11/r-package-devel/), for general venues where to ask for help about package development. My favorite ones are the [rOpenSci forum](https://discuss.ropensci.org/) and the [package development category of the RStudio community forum](https://community.rstudio.com/c/package-development/11).

## Reading other people's debugging journeys, document yours

Sadly but understandably people will often only take the time to document their debugging journey when the bug is especially tricky or weird. However, few people write actual [debugging games](https://jvns.ca/blog/2021/04/16/notes-on-debugging-puzzles/).

In the meantime, you might enjoy watching or hearing some debugging journeys. You will notice how these programmers make and invalidate hypotheses.

-   [Kara Woo's talk "Box plots - A case study in debugging and perseverance"](https://www.rstudio.com/resources/rstudioconf-2019/box-plots-a-case-study-in-debugging-and-perseverance/);

-   ["A debugging journey" by Jim Hester](https://www.jimhester.com/post/2018-03-30-debugging-journey/);

-   ["Debugging: Signals and Subprocesses" by Gábor Csárdi](https://blog.r-hub.io/2020/02/20/processx-blocked-sigchld/);

-   ["Debugging and Fixing CRAN's 'Additional Checks' errors" by Rich FitzJohn](https://reside-ic.github.io/blog/debugging-and-fixing-crans-additional-checks-errors/);

-   ["Debugging memory errors with valgrind and gdb" by Rich FitzJohn](https://reside-ic.github.io/blog/debugging-memory-errors-with-valgrind-and-gdb/).

If you end up documenting your own code detective story, please tell me, I'd like to read it!

## Conclusion

In this post I presented various techniques useful for code detectives. Getting better at them will help you debug and amend codebases. Now, I was able to summarize these tips, but I can't say I never get stuck. 🙂

### Where next?

If you have no personal opinion on what to "study" next from the numerous links of this post or elsewhere, my recommendation would be:

-   Start with Kara Woo's talk ["Box plots - A case study in debugging and perseverance"](https://www.rstudio.com/resources/rstudioconf-2019/box-plots-a-case-study-in-debugging-and-perseverance/) as it's both a real and engaging story (with closure i.e. the bug was fixed! happy end!) and covers many useful debugging tools.
-   Continue with the [Debugging advice in the Advanced R book by Hadley Wickham](https://adv-r.hadley.nz/debugging.html) that is very clear and well organized. "Advanced R" might a frightening title but really don't be afraid.
-   If you are an RStudio IDE user, you'll find great use of [Amanda Gadrow's webinar about debugging techniques in RStudio](https://www.rstudio.com/resources/webinars/debugging-techniques-in-rstudio/) and the [corresponding official RStudio documentation](https://support.rstudio.com/hc/en-us/articles/205612627-Debugging-with-the-RStudio-IDE).
-   Then you could watch Jenny Bryan's talk ["Object of type 'closure' is not subsettable"](https://github.com/jennybc/debugging#readme) as it covers a lot of ground around problem solving in R, with ideas extremely well conveyed (and if you want to binge watch more talks of Jenny Bryan's, continue with ["Code feels, code smells"](https://github.com/jennybc/code-smells-and-feels) that'll help you write better code to start with 🙂).
-   Lastly, you might be interested in drawing your own lessons from non R specific resources such as [the All Things Git podcast episode with Patricia Aas](https://www.allthingsgit.com/episodes/learning_a_new_codebase_with_patricia_aas.html), [Julia Evans' blog posts about debugging](https://jvns.ca/#debugging) and [Julia Evans' comics about debugging](https://wizardzines.com/zines/bugs/).

And then, just wait for the next problem to tackle in your coding practice... One never has to wait very long. 😅

### Last words

Last but not least I want to emphasize that there are also human aspects to this process.

{{< tweet 1403405539971842052 >}}
{{< tweet 1385573317277532162 >}}

What are your favourite tips and resources? Are you too eagerly awaiting [Julia Evans' zine about debugging](https://wizardzines.com/zines/bugs/)? Please tell me in the comments below!

[^1]: Travis CI itself is no longer recommended [by rOpenSci for instance](https://ropensci.org/blog/2020/11/19/moving-away-travis/).

