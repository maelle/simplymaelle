---
title: "How to translate your package's messages with {potools}"
date: '2023-10-06'
slug: potools-mwe
output: hugodown::hugo_document
rmd_hash: 48d31a2a23fdc3e2

---

*This post was featured on the [R Weekly highlights podcast](https://rweekly.fireside.fm/140) hosted by Eric Nantz and Mike Thomas.*

In November I'll give a talk about multilingualism in R at the [Spanish R conference](https://eventum.upf.edu/101896/programme/ii-conference-of-r-and-xiii-workshop-for-r-users.html) in Barcelona (:heart_eyes:). I can't wait! Until then, I need to prepare my talk. :sweat_smile: I plan to present [the rOpenSci "multilingual publishing" project](https://ropensci.org/multilingual-publishing/) but also other related tools, like potools. In this post, I'll walk you through a minimal example of using potools to translate messages in an R package!

## What is potools?

In R, you can provide messages in different languages by using .po, .pot and .mo files. If you provide messages in English and their translation to Spanish, an user who sets the environment variable `LANGUAGE` to es will get to see Spanish messages rather than the default English messages.

The [potools package by Michael Chirico](https://michaelchirico.github.io/potools/) is the roxygen2 of .po, .pot and .mo files: as you could well write Rd files by hand[^1], you could write the translation files by hand... but things are easier with potools.

## Create package

I create a package called [pockage](https://github.com/maelle/pockage), with my usual usethis workflow. It initially has a single function, that tries to extract the user's name via the whoami package, and then says hello using the cli package:

``` r
#' Say hello
#'
#' @return Nothing
#' @export
#'
#' @examples
#' speak()
speak <- function() {
  name <- whoami::fullname(fallback = "user")
  cli::cli_alert_info("Hello {name}!")
}
```

Let's try it out:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>pockage</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/pockage/man/speak.html'>speak</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Hello Maëlle Salmon!</span></span>
<span></span></code></pre>

</div>

## Install potools

I install potools from GitHub using `pak::pak("MichaelChirico/potools")`.

## Register potools style in DESCRIPTION

I shall use potools explicit style, which means it will recognize strings as translatable only if I mark them as so. Therefore I add these lines to `DESCRIPTION`:

    Config/potools/style: explicit

## Create `tr_()` function and use it

Following [potools' vignette for developers](https://michaelchirico.github.io/potools/articles/developers.html), I run `usethis::use_r("utils-potools")` and paste the definition an internal function in that new file:

``` r
tr_ <- function(...) {
  enc2utf8(gettext(paste0(...), domain = "R-pockage"))
}
```

I then modify the `speak()` function:

``` r
#' Say hello
#'
#' @return Nothing
#' @export
#'
#' @examples
#' speak()
speak <- function() {
  name <- whoami::fullname(fallback = "user")
  cli::cli_alert_info(tr_("Hello {name}!"))
}
```

The difference is that the string "Hello {name}!" is now marked as translatable.

## Create the general translation file

I run [`potools::po_extract()`](https://michaelchirico.github.io/potools/reference/po_extract.html) to create the `po/R-pockage.pot` file.

## Create the translation file for French and fill it

Then I run `potools::po_create("fr")` to create the file holding the translation of the string to French.

I obtain this `po/R-fr.po` file where I edit two lines (Last-Translator, and msgstr at the bottom):

    msgid ""
    msgstr ""
    "Project-Id-Version: pockage 0.0.0.9000\n"
    "POT-Creation-Date: 2023-10-06 10:45+0200\n"
    "PO-Revision-Date: 2023-10-06 10:33+0200\n"
    "Last-Translator: Malle Salmon\n"
    "Language-Team: none\n"
    "Language: fr\n"
    "MIME-Version: 1.0\n"
    "Content-Type: text/plain; charset=ASCII\n"
    "Content-Transfer-Encoding: 8bit\n"
    "Plural-Forms: nplurals=2; plural=(n > 1);\n"

    #: mensaje.R:9
    msgid "user"
    msgstr "utilisateur·rice"

    #: mensaje.R:10
    msgid "Hello {name}!"
    msgstr "Salut {name} !"

## Compile the translation

I compile with [`potools::po_compile()`](https://michaelchirico.github.io/potools/reference/po_compile.html) which creates the `.mo` binary for the French language in `inst/po`.

## Try out the translation

I load or install&load the package, then I use the following code, magically using `withr` to set the `LANGUAGE` environment variable locally:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_language.html'>with_language</a></span><span class='o'>(</span><span class='s'>"fr"</span>, <span class='nf'>pockage</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/pockage/man/speak.html'>speak</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Salut Maëlle Salmon !</span></span>
<span></span><span><span class='c'># as opposed to</span></span>
<span><span class='nf'>pockage</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/pockage/man/speak.html'>speak</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Salut Maëlle Salmon !</span></span>
<span></span></code></pre>

</div>

Quite neat, right?

## Add languages

I then add two other languages, unstoppable as I am now, by running `potools::po_create(c("es", "ca"))`. I add the Spanish and Catalan translations of the string in respectively `po/R-es.po` and `po/R-ca.po`. I compile with [`potools::po_compile()`](https://michaelchirico.github.io/potools/reference/po_compile.html) which creates the `.mo` binary for the Spanish and Catalan languages in `inst/po`.

After installing the pockage package again, I get:

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_language.html'>with_language</a></span><span class='o'>(</span><span class='s'>"fr"</span>, <span class='nf'>pockage</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/pockage/man/speak.html'>speak</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Salut Maëlle Salmon !</span></span>
<span></span><span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_language.html'>with_language</a></span><span class='o'>(</span><span class='s'>"es"</span>, <span class='nf'>pockage</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/pockage/man/speak.html'>speak</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Hola Maëlle Salmon!</span></span>
<span></span><span></span>
<span><span class='c'># I swear it's pronounced differently</span></span>
<span><span class='nf'>withr</span><span class='nf'>::</span><span class='nf'><a href='https://withr.r-lib.org/reference/with_language.html'>with_language</a></span><span class='o'>(</span><span class='s'>"ca"</span>, <span class='nf'>pockage</span><span class='nf'>::</span><span class='nf'><a href='https://rdrr.io/pkg/pockage/man/speak.html'>speak</a></span><span class='o'>(</span><span class='o'>)</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; <span style='color: #00BBBB;'>ℹ</span> Hola Maëlle Salmon!</span></span>
<span></span></code></pre>

</div>

## Translate more strings

Now imagine I also want to translate the fallback of the whoami function call, for the case whoami can't identify the user.

``` r
#' Say hello
#'
#' @return Nothing
#' @export
#'
#' @examples
#' speak()
speak <- function() {
  name <- whoami::fullname(fallback = tr_("user"))
  cli::cli_alert_info(tr_("Hello {name}!"))
}
```

I run

-   [`potools::po_extract()`](https://michaelchirico.github.io/potools/reference/po_extract.html) again;
-   then [`potools::po_update()`](https://michaelchirico.github.io/potools/reference/po_update.html);
-   after which I need to go add a msgstr under `msgid "user"` in `po/R-fr.po`, `po/R-es.po`, `po/R-ca.po`;
-   finally I run [`potools::po_compile()`](https://michaelchirico.github.io/potools/reference/po_compile.html).

## Conclusion

Find my final pockage package on [GitHub](https://github.com/maelle/pockage).

To translate messages with potools in the explicit style one needs to:

-   register the potools style in `DESCRIPTION`;
-   create and use a `tr_()` internal function to wrap strings to be translated;
-   run [`potools::po_extract()`](https://michaelchirico.github.io/potools/reference/po_extract.html) at the beginning of the translation efforts and every time strings wrapped in `tr_()` are changed, deleted, added;
-   run [`potools::po_create()`](https://michaelchirico.github.io/potools/reference/po_create.html) once per non-default language to be supported;
-   run [`potools::po_update()`](https://michaelchirico.github.io/potools/reference/po_update.html) after [`potools::po_extract()`](https://michaelchirico.github.io/potools/reference/po_extract.html) every time strings wrapped in `tr_()` are changed, deleted, added;
-   run [`potools::po_compile()`](https://michaelchirico.github.io/potools/reference/po_compile.html) every time the translation source files are changed.

potools has one vignette for [developers](https://michaelchirico.github.io/potools/articles/developers.html) and one for [translators](https://michaelchirico.github.io/potools/articles/translators.html) that I'd recommend reading, because they provide useful advice beyond the basic workflow that I illustrated here.

[^1]: which is what I first learnt to do years and years ago.

