---
title: "Reading notes on Code That Fits in Your Head by Mark Seemann"
date: '2026-08-14'
slug: code-fits-head-mark-seemann-reading-notes
output: hugodown::hugo_document
tags:
  - good practice
  - books

---

Last month, Vicki Boykis [recommended](https://bsky.app/profile/vickiboykis.com/post/3mpk5n2velk2i) the book "Code that Fits in Your Head" by Mark Seemann.
I was intrigued, especially by her takeaway that "Writing good software should be a slow and deliberate craft".

The book reminded of [The Pragmatic Programmer](/2023/12/11/reading-notes-pragmatic-programmer/) in the breadth of topics it covers.
However, I much preferred its tone.

## Sustainability

Throughout the book, the author makes it clear that he believes one should pay attention to the code one writes, to its internal quality.
The following two sentences even got their little frame:

> "The goal is not to write code fast. The goal is sustainable software."

## Arrange Act Assert

What  useful phrase!

You know how in a test you can have something like:

```r
test_that("Exception Bla handled", {
  withr::local_option("bla" = 1)
  test_thing <- 23

  x <- my_function(test_thing)

  expect_equal(x, 2)
})
```

The first two lines within the `test_that()` call prepare what's needed. That's the *arrange* phrase.

The line calling `my_function()` does the thing I want to test. That's the *act* phrase.

The line calling `expect_equal()` checks that thing. That's the *assert* phrase.

Arrange, act, assert!
The book even mentions you can separate the three phases with empty lines.

## Red Green Refactor

Another neat phrase! 
It describes how you could iteratively work on a part of your software.

- Red: you write a failing test for e.g. a feature.
- Green: you write the code to make the test pass.
- Refactor: you improve that code (but don't make the test fail again!).

I appreciated how the author wrote that even the red phase isn't easy, because you can get a passing test that you thought would fail.
I think the point is made again elsewhere in the book: you can imagine a test will pass or fail under certain conditions, but better to actually prove it by [running the test](/2024/08/29/cherrypick-test/)!

## The Devil's Advocate

A technique presented in the book is The Devil's Advocate, in which you write wrong code on purpose, to see whether your tests will detect it.
I suppose it's a less random (but more difficult?) version of mutation testing, in which you evaluate how often your test suite detects mutants of your code.
In R, mutation testing is provided by e.g. the [mutator package](https://prl-prg.github.io/mutator/).

## Code reviews that do not block

There's a short discussion of when to do code reviews (regularly) so as to not block your team mates.
I enjoyed seeing this, as it's important to not always be a rate-limiting factor.
It shows the book's advice is pragmatic.

## Bundle smaller breaking changes

In the chapter "Augmenting code" that's about working with existing code, the author -- among other things -- discusses whether to bundle or separate breaking changes into one or several releases.
The decision criterion is how much work you create for "client developers" (maintainers of reverse dependencies).

It's also one of the many places in the book where the author reminds us there's no hard rule, that it's the "_art_ of software engineering".

## House or not House

The first chapter of the book presents and criticizes analogies of software engineering, like comparing developing software to building a house.
That reminded of the talk [Maintaining the house the tidyverse built](https://resources.rstudio.com/resources/rstudioglobal-2021/maintaining-the-house-the-tidyverse-built/) by Hadley Wickham, that compared tidyverse maintenance to house maintenance.

The chapter's thesis is that all analogies are useful and imperfect and you shouldn't let them limit or cloud the way you view your work.

## Justify exceptions

At some point the author writes that static code analysis brings false positives, but that if you disable specific rules, you should justify why.
That's how [Jarl](https://blog.r-hub.io/2026/06/02/jarl/), a linter for R code, makes you suppress rules: you have to explain a [reason](https://jarl.etiennebacher.com/howto/suppression-comments).

## Git is easy

I like these encouraging three sentences:

> "Git isn't the most user-friendly piece of technology on the planet, but you're a programmer. You've managed to learn at least one programming language. Compared to that, learning the basics of Git is easy."

## Testing against databases

As an example of slow tests, the author mentions tests against databases.
I want to use this as an excuse to plug the [dittodb package](https://docs.ropensci.org/dittodb/) to mock databases in tests.

## Bisection without and with Git

In the chapter about Troubleshooting, the idea of bisection without Git is introduced.
The author says it's called bisection for lack of a better word.
The idea being to go from a big piece of code with a bug to the smallest piece of code with the same bug,
I suppose a better word is [reprex](https://reprex.tidyverse.org/). :grin:

> "Being able to produce a minimal working example is a superpower in software engineering."

Regarding _Git_ bisection, here's your friendly reminder that you can try it out using the [saperlipopette R package](https://docs.ropensci.org/saperlipopette/reference/exo_bisect.html).

## Time boxing

The author extols the virtues of time boxing (e.g. the Pomodoro method).
I've been using [Entracte](https://entracte.drmowinckels.io/) and would recommend giving it a go, if you have no similar setup yet!

When reading the book, between chunks of reading I'd [crochet a few rounds](/2026/05/19/crochet-again/).

## Performance

I enjoyed the pragmatic discussion of performance: don't forget to check the numbers and their significance.

## Behavioural code analysis

I have never used behavioural code analysis, something that uses Git data, but its presentation reminded me of [Git commands to get to know a project](https://ropensci.org/blog/2026/04/30/news-april-2026/#git-commands-to-get-to-know-a-project) which is related (or the same?).

## Code navigation

Yay to the mention of learning how to navigate code in your IDE. 
Such an important skill.

## Conclusion

"Code that Fits In Your Head" is an ambitious (and rather long) book.
I was glad to learn new phrases and to get the opportunity to get to know or reflect on so many topics. 

Because the book was published in 2021, AI only makes an appearance as a brief footnote.
The only moment I really thought AI would make a point less valid was a point about some kinds of refactoring taking ages: in the igraph R package, some changes have recently become feasible in an easier way thanks to using tools like Claude.

Some aspects of the book might be a tad annoying like the super simple diagrams or illustrations (think: a hammer drawing to illustrate the fact that to someone with a hammer, everything looks like a nail) but I suppose those play their role of breaking up pages.

## Bonus: a reading list

If someone fairly new to software engineering were to ask me book recs, from looking at the stack near my desk I would recommend the following books, more specialized than "Code that Fits In Your Head":

-  [The Art of Readable Code by Dustin Boswell and Trevor Foucher](https://www.oreilly.com/library/view/the-art-of/9781449318482/)
- [The Programmer's Brain by Felienne Hermans](https://www.manning.com/books/the-programmers-brain) (that I plan to re-read soon!)
- [A Philosophy of Software Design by John Ousterhout](/2023/10/19/reading-notes-philosophy-software-design/)
- A Git book, either [Git in Practice by Mike McQuaid](/2023/11/01/reading-notes-git-in-practice/) or [Pro Git by Scott Chacon](/2024/01/19/pro-git-scott-chacon-reading-notes/)
- A book about team work. Maybe [The Psychology of Software Teams by Cat Hicks](https://www.routledge.com/The-Psychology-of-Software-Teams/Hicks/p/book/9781032963389), [The Collective Edge by Colin M. Fisher](https://colinmfisher.com/), [Dare to Lead by Brené Brown](https://brenebrown.com/hubs/dare-to-lead/)...
- A book about productivity/organization, but not a sanctimonious one about profound labor or the like. I enjoyed [Make Time by Jake Knapp and John Zeratsky](https://maketime.blog/) and [Tranquility by Tuesday by Laura Vanderkam](https://lauravanderkam.com/books/tranquility-by-tuesday/).
- A book critical of AI such as [The AI Con by Emily M. Bender and Alex Hanna](https://thecon.ai/) or [Empire of AI by Karen Hao](https://en.wikipedia.org/wiki/Empire_of_AI).

I also believe there are other ways to learn, like watching talks, but this post is about books. :smile_cat:


