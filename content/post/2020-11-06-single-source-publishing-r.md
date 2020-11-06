---
title: "Single-source publishing for R users"
date: '2020-11-06'
tags:
  - css
  - hugo
  - rmarkdown
slug: single-source-publishing-r
---

A big part of my work includes putting content about R online, in blog posts and online books.
I'm therefore very interested in the technical infrastructure that allows us R users to produce beautiful products out of R Markdown or Markdown source files.
In this post I shall summarize my recent experiments around making HTML and PDF versions of books.
Thanks to [Julie Blanc's inspiring post in French](https://julie-blanc.fr/blog/2020-11-05_chiragan/), I have learnt this is called [_single-source publishing_](https://en.wikipedia.org/wiki/Single-source_publishing).
So let's dive in the [incredible machines](https://en.wikipedia.org/wiki/The_Incredible_Machine_%28series%29) allowing single-source publishing for R users. :wink:

## What I want for writing books

~~I want technical tools that allow me to procrastinate a lot whilst learning a ton.~~
Just kidding, or not. :innocent:
My official criteria are:

* For a book(let), I want a beautiful, usable website as well as a beautiful, usable PDF since some folks might prefer that for printing or offline usage.
* I want to be able to write content in R Markdown or Markdown files, with some helpers for elements that Markdown doesn't allow, for instance I might use a custom block to show an alert.
* I want continuous deployment for my books, using git and a continuous integration service, so that every change in the source is reflected in the online versions of my book without my needing to think about it.
* I would prefer to knit R Markdown files only once.
* I don't want to have to learn two ways to style things. [^website]

[^website]: Yes, I should probably re-style this very website.

Now, let's dive into three workflows I've used or experimented with.

## The standard way: bookdown gitbook and pdf_book

We R users are very lucky to be able to use bookdown for making books.
You write content in Rmd files in a single folder, you order their filenames in a config file and choose an output format when knitting.
There are many books out there built using bookdown, and often they're knit twice so that there's an HTML and PDF version.
See for instance [rOpenSci dev guide](https://devguide.ropensci.org), that is deployed with GitHub Actions.

Advantages of bookdown include its widespread usage and stability, as well as its docs, both by its authors and users. For instance:

* There is a [bookdown book](https://bookdown.org/yihui/bookdown/) by bookdown maintainer Yihui Xie.
* Emil Hvitfeldt wrote a post about [](https://www.hvitfeldt.me/blog/bookdown-netlify-github-actions/).

A downside of bookdown ~~for the procrastinators out there~~ is, in my opinion, that if you want a PDF and HTML versions of a book, you have to knit it twice.
Furthermore, if you introduce some fancy things such as a custom block, you will have to define it both with CSS and with LaTeX environments.
Now, I have written a [whole book with LaTeX](https://www.editions-ellipses.fr/accueil/5374-l-oral-de-biologie-aux-concours-bcpst-9782729853693.html) so I used to partially know how to use it, but these days I'd prefer learning about CSS only as there's already a lot to digest in there. :sweat_smile:

So let's dive into two DIY ways of publishing a book in HTML and PDF.

## Hugo and pagedjs-cli

In this proof-of-concept, content is stored in Markdown files, that could have been generated with hugodown.
Actors are [Hugo](https://gohugo.io/), a powerful static website generator that you get in the form of a binary, and [pagedjs-cli](https://gitlab.pagedmedia.org/tools/pagedjs-cli), a Node package that prints HTML to PDF.

You can find a [repo corresponding to this experiment](https://github.com/maelle/testbook) on GitHub, and the [resulting book](https://hugo-pagedjs-book.netlify.app/) on Netlify.

This proof-of-concept is based on the great [hugo-book theme](https://github.com/alex-shpak/hugo-book). 
My "work" was just to add a bit of setup magic (Netlify config, config/ dir).

The idea is to run two Hugo builds with [different configurations](https://gohugo.io/getting-started/configuration/).
In the [words of Hadley Wickham](https://github.com/r-lib/hugodown/issues/14#issuecomment-632850506), _"hugo provides a bewildering array of possible config locations"_. 
In this proof-of-concept/incredible machine, this array of possibilities is key to success.
The two configurations use different layout directories, layout directories being the templates telling Hugo where to put your words, e.g. every blog post on its own page, and a page showing summaries of all of them.

* One Hugo build, `hugo -d public --environment 'website'`, creates the HTML website with a link to "book.pdf" somewhere, in the `public/` folder.
* The second one, `hugo -d public2 --environment 'pdf'`, creates a website with a single HTML page containing _all_ the book content.
* Then that single page is printed to PDF using pagedjs-cli and copied as "book.pdf" to public/. `pagedjs-cli public2/all/index.html -o public/book.pdf"`
* The live website uses the content of the `public/` folder so it has the website and the PDF.

Netlify can run all these commands

```toml
[build]
publish = "/public"
command = "hugo -d public --environment 'website' && hugo -d public2 --environment 'pdf' && pagedjs-cli public2/all/index.html -o public/book.pdf"
```

The dependency on pagedjs-cli is indicated with packages.json.

Now if the content lived in R Markdown files, we would need to use a service like GitHub Actions to knit things.

### Advantages

* The Markdown files that are the source of both versions of the book would be generated only once if you started from R Markdown.
* Styling both versions rely on CSS. I haven't added any print styles but if you used this workflow for real, then it'd be better to, of course.
* Compared to creating a gitbook with `bookdown`, one could customize the HTML website more easily, and use any Hugo feature.

### Further work

If I decided to take this further, that is!
All the work would be aimed at making the PDF version better

* Improve the Hugo layout to add various tables of contents (for the whole book, for chapters).
* Actually work on the CSS print sheet for the PDF version.
* Make Katex, Mermaid etc. work for the PDF version.

### Related work

The work presented by [Julie Blanc](https://julie-blanc.fr/blog/2020-11-05_chiragan/) is a similar pipeline, where content lives in Markdown files rendered to two HTML versions by Jekyll, another static website generator.
One HTML version is a website, the other one is a printable version thanks to Paged.js (the PDF is not pre-generated but you can get it from any modern browser).
The big differences with my experiment are:

* Julie Blanc's work is not an experiment, it's deployed to production!
* Both versions of the book are absolutely stunning, there was actual CSS work involved. 

## bookdown::bs4_book(), xml2 and pagedjs-cli

In another experiment, I used bookdown's brand-new HTML book format, `bs4_book()` by Hadley Wickham (only available in bookdown's dev version).
I used this one because I find it looks better than gitbook, and because it uses Bootstrap, you get to use divs such as a Bootstrap alerts.

The [source](https://github.com/maelle/bspagedjs) is open.
[HTML version](https://maelle.github.io/bspagedjs/intro.html) and [PDF version](https://maelle.github.io/bspagedjs/result.pdf). 
This time I took time to learn a bit about print CSS. :smile_cat:

Here the steps are knit using the `bs4_book()` template for HTML and then for getting a PDF

* tweaks of the HTML e.g. moving the chapter table of contents from the sidebar to the main content, and merging of all chapters using xml2 and XPath (so yeah you're ditching LaTeX in favor of a workflow involving XPath, so modern :upside_down_face:) in [build.R](https://github.com/maelle/bspagedjs/blob/master/build.R).
* some print CSS, see [stylesheet](style.css). It turns out that [following Paged.js docs about print CSS](https://www.pagedjs.org/documentation/05-designing-for-print/) is not that hard.
* pagedjs-cli.

Locally I sourced build.R to see whether it works, but then I also wrote a [GitHub Actions workflow](https://github.com/maelle/bspagedjs/blob/master/.github/workflows/main.yml) that installs dependencies, runs the script, and deploys the docs/ folder to a gh-pages branch.


Note that Bootstrap provides [classes you can add to elements for changing their display in print](https://getbootstrap.com/docs/4.0/utilities/display/#display-in-print).
So to not show the burger menu in print, you can either do as I did with a CSS property

```css
@media print {
  .btn.btn-outline-primary.d-lg-none.ml-2.mt-1 {
    display: none;
}

}
```

or you'd add the `.d-print-none` to that element in your HTML template.
If you can control said HTML template, that is.

## Conclusion

So, as an R user writing content in R Markdown, you have different possibilities for respecting the principles of single-source publishing.
I would still recommend using bookdown or other official rmarkdown formats, knitting twice[^cache] if you need two versions, for now as it is the only stable and widely used solution, but I found it fun to explore other workflows.
Both the DIY ways I presented use pagedjs-cli to generate a PDF out of HTML. I strongly recommend following the work done by the [lovely Paged.js folks](https://www.pagedjs.org/) in the world of paged media, and to learn more about CSS for print.
If you want to produce only paged content out of your R Markdown file, check out the [pagedown package](https://pagedown.rbind.io/) and its fantastic output formats.

If you want to go another way and experiment with homemade pipelines, have fun customizing things. :wink:
Now, of course, the possibility of customization might actually be a curse when you are trying to write something.
But this was not a blog post about productivity. :grin:

[^cache]: Potentially, knitr cache might make knitting a thing a second time very fast? :thinking_face: