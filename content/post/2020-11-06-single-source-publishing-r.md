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

I want technical tools that allow me to procrastinate a lot whilst learning a ton.
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

Advantages of bookdown include its widespread usage and stability, as well as its docs, both by its authors and users.
* There is a [bookdown book](https://www.bookdown.org/)
For a walk-through about how to 
