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
So let's dive in the incredible machines allowing single-source publishing for R users. :wink:

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

A downside of bookdown ~~for the procrastinators out there~~ is, in my opinion, that if you want a PDF and HTML versions of a book, you have to need it twice.
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
The two configurations use a different layout directories so the content is rendered in a different way.

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
* Styling both versions rely on CSS.
* Compared to creating a gitbook with `bookdown`, one could customize the HTML website more easily, and use any Hugo feature.

### Further work

If I decided to take this further, that is!

* Actually work on the CSS print sheet for the PDF version.
* Make Katex, Mermaid etc. work for the PDF version.



