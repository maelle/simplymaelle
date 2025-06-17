---
title: "How to open files, folders, websites in R"
date: '2025-06-19'
slug: cover-modify-r-packages
output: hugodown::hugo_document
tags:
  - good practice
  - code style
  - useful functions
rmd_hash: ffc32e5c5a93f92d

---

Coming to you from France, a post about [*Mise en place*](https://en.wikipedia.org/wiki/Mise_en_place) for R projects. In a less francophone phrasing: to get to work on something you have to open that thing, be it a script or a project or a website. The easier that is, the faster you get to work. In this post I'll show a roundup of R functions and related tools for opening scripts, projects and websites for yourself or on behalf of the user of your code.

*Many thanks to [Hannah Frick](https://www.frick.ws/) for providing inspiration for some items of this post!*

## Open any file in the editor: `utils::file.edit()`, {cli}

If you write code that creates a file at `path` and then is supposed to open it for the user, no need for you to use, say, `rstudioapi::documentOpen(path)` that only works in [RStudio IDE](https://posit.co/products/open-source/rstudio/). You can use a base R function, [`utils::file.edit()`](https://rdrr.io/r/utils/file.edit.html)! It will open the path in the default editor. Without my setting up anything, in RStudio IDE that is RStudio IDE and in [Positron](https://drmowinckels.io/blog/2025/positron-debugging/) that is Positron. From an R session in the terminal[^1], that is the default editor of my system[^2].

I was surprised to see that [`usethis::edit_file()`](https://usethis.r-lib.org/reference/edit_file.html) uses an RStudio specific function when available:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='c'># https://github.com/r-lib/usethis/blob/4aa55e72ccca131df2d98fcd84fff66724d6250a/R/edit.R#L32C1-L36C4</span></span>
<span><span class='kr'>if</span> <span class='o'>(</span><span class='nf'>rstudio_available</span><span class='o'>(</span><span class='o'>)</span> <span class='o'>&amp;&amp;</span> <span class='nf'>rstudioapi</span><span class='nf'>::</span><span class='nf'><a href='https://rstudio.github.io/rstudioapi/reference/hasFun.html'>hasFun</a></span><span class='o'>(</span><span class='s'>"navigateToFile"</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'>rstudioapi</span><span class='nf'>::</span><span class='nf'><a href='https://rstudio.github.io/rstudioapi/reference/navigateToFile.html'>navigateToFile</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span> <span class='kr'>else</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'>utils</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/r/utils/file.edit.html'>file.edit</a></span><span class='o'>(</span><span class='nv'>path</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

According to the [commit that added this logic](https://github.com/r-lib/usethis/commit/9ab2582980f0f4a8a1d565dba00345ac7aa7e2a2) more than 7 years ago, "utils::file.edit opens in dialog" which I do not understand. Maybe it's different depending on the OS? Maybe RStudio changed in the meantime? Please tell me if you have any more information on this. :pray:

In any case I really enjoy [`file.edit()`](https://rdrr.io/r/utils/file.edit.html) in codebases. Interactively, I do not use is as often as, say, Positron's shortcut for navigating to files (Ctrl+P on my machine).

Last but not least, if you want to make it easy for the user to open a file, without opening it on their behalf, in messages emitted through the [cli package](https://blog.r-hub.io/2023/11/30/cliff-notes-about-cli/) you can use the [file class](https://cli.r-lib.org/reference/inline-markup.html#classes):

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>cli</span><span class='nf'>::</span><span class='nf'><a href='https://cli.r-lib.org/reference/cli_alert.html'>cli_alert_success</a></span><span class='o'>(</span><span class='s'>"Hey go edit &#123;.file config.toml&#125; please!"</span><span class='o'>)</span></span></code></pre>

</div>

> *"If the terminal supports ANSI hyperlinks (e.g. RStudio, iTerm2, etc.), then cli creates a clickable link that opens the file in RStudio or with the default app for the file type."*

## Open the script or test file of a script-test pair: `usethis::use_r()` and `usethis::use_test()`

Coming back to the wonderful usethis package, when you name your test files after your scripts as you should, running [`usethis::use_test()`](https://r-pkgs.org/testing-basics.html#create-a-test) when focussed on an R script will open its test file, and conversely [`usethis::use_r()`](https://usethis.r-lib.org/reference/use_r.html) when focussed on a test file. This is still how I switch between the two even if inside Positron.

## Open a project: `positron`, project launcher, {usethis}

This is all very good when within a project, but how do you enter your IDE in the first place? How sad would it be to lose momentum even before launching a project?

When I used RStudio IDE I would navigate to the folder of interest then double-click on the `.Rproj` file. Hannah Frick inspired me, when demoing Positron to me, to think a bit harder about how much I click. :sweat_smile: I have adopted a more keyboard-based workflow since I switched to Positron, which I could have done with RStudio IDE.

One thing in particular that Hannah showed me that looks cool is the use of a [project launcher](https://positron.posit.co/rstudio-rproj-file.html#use-an-application-launcher).

Now, I myself still use the terminal. For instance if I clone a repository[^3], I'll then open it with the `positron` command.

Another way to open projects I use a lot is, on Positron as on Rstudio, clicking to the recent projects -- when the IDE is already up and running. Unfortunately, I created and [currently](https://stateofther.netlify.app/post/saperlipopette/) [often](https://www.meetup.com/rbuenosaires/events/308338205/) use a [package that creates throwaway folders for practicing Git](https://docs.ropensci.org/saperlipopette/) which makes the list of recent projects utter rubbish. 🙃

Within saperlipopette itself, to create and open the exercice folder on behalf of the user I use [`usethis::create_project()`](https://usethis.r-lib.org/reference/create_package.html), which handily opens it in Positron when run in Positron and RStudio when run in RStudio.

## Open an URL in the browser: `browseURL()` and {cli}

Sometimes you want the user of some code of yours to go admire or read a web page.

To open an URL in the default browser, you can use [`utils::browseURL()`](https://rdrr.io/r/utils/browseURL.html). In all of the `usethis::browse_` functions, [`utils::browseURL()`](https://rdrr.io/r/utils/browseURL.html) is what's used under the hood. For instance:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/utils/browseURL.html'>browseURL</a></span><span class='o'>(</span><span class='s'>"https://masalmon.eu/post"</span><span class='o'>)</span></span></code></pre>

</div>

To make it easy to open an URL from a message, you can use the [URL class](https://cli.r-lib.org/reference/inline-markup.html#classes) of the cli package. For instance:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>cli</span><span class='nf'>::</span><span class='nf'><a href='https://cli.r-lib.org/reference/cli_alert.html'>cli_alert_info</a></span><span class='o'>(</span><span class='s'>"Go read that blog! &#123;.url https://masalmon.eu/post&#125;!"</span><span class='o'>)</span></span></code></pre>

</div>

When getting that message, the user will simply have to click on the link to follow it (after trusting the domain in Positron).

## Open only in interactive sessions: `is.interactive()`, `rlang::is_interactive()`

If you use [`utils::file.edit()`](https://rdrr.io/r/utils/file.edit.html) or [`utils::browseURL()`](https://rdrr.io/r/utils/browseURL.html) in your code, you need to ensure the session is interactive.

Either

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'>if</span> <span class='o'>(</span><span class='nf'>is.interactive</span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/utils/file.edit.html'>file.edit</a></span><span class='o'>(</span><span class='s'>"config.toml"</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

Or

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='kr'>if</span> <span class='o'>(</span><span class='nf'>rlang</span><span class='nf'>::</span><span class='nf'><a href='https://rlang.r-lib.org/reference/is_interactive.html'>is_interactive</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span> <span class='o'>&#123;</span></span>
<span>  <span class='nf'><a href='https://rdrr.io/r/utils/file.edit.html'>file.edit</a></span><span class='o'>(</span><span class='s'>"config.toml"</span><span class='o'>)</span></span>
<span><span class='o'>&#125;</span></span></code></pre>

</div>

Compared to `is.interactive()`, [`rlang::is_interactive()`](https://rlang.r-lib.org/reference/is_interactive.html) also checks whether knitr or testthat are in progress, and provides an escape hatch through the `rlang_interactive` option.

All of usethis functions that try to open or browse something on behalf of the user behave differently based on [`rlang::is_interactive()`](https://rlang.r-lib.org/reference/is_interactive.html). [`usethis::edit_file()`'s source](https://github.com/r-lib/usethis/blob/4aa55e72ccca131df2d98fcd84fff66724d6250a/R/edit.R#L20), [`usethis::view_url()`'s source](https://github.com/r-lib/usethis/blob/4aa55e72ccca131df2d98fcd84fff66724d6250a/R/helpers.R#L107).

## Conclusion

In this post I summarized some tools for opening scripts, projects, URLs for yourself or on behalf of your user.

| Target to open          | Tool                                                                                              | Audience                                 |
| ----------------------- | ------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| File                    | [`utils::file.edit()`](https://rdrr.io/r/utils/file.edit.html)                                                                            | User of your code                        |
| \-                      | IDE's shortcut                                                                                    | You -- get to know your IDE              |
| \-                      | [`.file` class of cli message](https://cli.r-lib.org/reference/inline-markup.html#classes) | User of your code                        |
| R script from test file | [`usethis::use_r()`](https://usethis.r-lib.org/reference/use_r.html)                                                                              | You                                      |
| \-                      | IDE's shortcut                                                                                    | You -- get to know your IDE              |
| Test file from R script | [`usethis::use_test()`](https://usethis.r-lib.org/reference/use_r.html) | You                                      |
| \-                      | IDE's shortcut                                                                                    | You -- get to know your IDE              |
| Project (folder)        | Rproj file                                                                                        | RStudio IDE user who like clicking       |
| \-                      | List of recent projects within the IDE                                                            | You when the IDE is already launched     |
| \-                      | `rstudio`, `positron`                                                                         | Terminal dwellers                        |
| \-                      | [Project launcher](https://positron.posit.co/rstudio-rproj-file.html#use-an-application-launcher) | Positron users on macOS                  |
| \-                      | [`usethis::create_project()`](https://usethis.r-lib.org/reference/create_package.html)                                                                     | User of your code that created a project |
| URL                     | [`utils::browseURL()`](https://rdrr.io/r/utils/browseURL.html)                                                                            | User of your code                        |
| \-                      | [`.url` class of cli message](https://cli.r-lib.org/reference/inline-markup.html#classes) | User of your code                        |

[^1]: Look how adventurous I was when preparing this post! Running R from a terminal!

[^2]: Which is... [Atom](https://en.wikipedia.org/wiki/Atom_(text_editor)), meaning I live in the past since its end-of-life occurred a few years ago. :ghost: I actually rarely use it.

[^3]: I know I could use [`usethis::create_from_github()`](https://usethis.r-lib.org/reference/create_from_github.html) and sometimes do but not always... Maybe the message of this post is that my workflows are a big mess.

