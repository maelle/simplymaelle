---
title: "Reading notes on The Pragmatic Programmer by David Thomas and Andrew Hunt"
date: '2023-12-11'
slug: reading-notes-pragmatic-programmer
output: hugodown::hugo_document
tags:
  - good practice
  - books
rmd_hash: df35a06122bcffe5

---

In my quest to having reading notes on the tech books I read, and while waiting for code to run, I recently re-read [The Pragmatic Programmer](https://pragprog.com/titles/tpp20/the-pragmatic-programmer-20th-anniversary-edition/) by David Thomas and Andrew Hunt. That book, whose second edition was published in 2019, offers an overview of many topics useful to programmers, from the idea of taking responsability, to testing, tooling, etc.

## Not my favorite tone

I'm not the biggest fan of the tone used in the book, that feels a bit patronizing to me. One example that comes to mind is that the authors recommend reading non-tech books to learn about humans, because programming is also about humans. That's not a piece of advice I'd give, even as a book lover, because one can learn about humans outside of books (not exactly breaking news, I know), and also because I'd like reading non-tech books to be a thing one can do for enjoyment unrelated to work.

I also disliked analogies that I found uselessly violent: car crashes, train wrecks, boiling frogs, not to forget [broken windows](https://en.wikipedia.org/wiki/Broken_windows_theory#Criticism). There's enough violence in the world, so I prefer further violence to inhabit my non-tech reading rather than my tech reading. :wink:

Now, to each their own, and I can imagine such writing being enjoyable to others.

## An impressive overview

The Pragmatic Programmer is rather famous. I can see why when I think about its table of contents: even if the book would not be my first recommendation for any topic it covers, it's the only one I know that covers so many topics: version control, testing, domain-specific languages, orthogonality, decoupling, etc. Seen this way, it's quite unique and precious.

Furthermore, I can imagine productive book clubs using the questions in the book.

## What value for me? Pieces of wisdom and catchy phrases

I did not read The Pragmatic Programmer as an absolute beginner, so I had at least vaguely heard of many (not all!) elements in the book. However, I find some value in being exposed to concepts I didn't know, seeing new explanations or catchy descriptions for those I was more familiar with, and thinking differently about current work projects (the two latter aspects are often the value I find in so-called "productivity books").

For most of the notes below I'm adding links to more or less related things, which would probably make me an annoying participant of a book club about The Pragmatic Programmer. Luckily this is my blog. :innocent:

### ETC

The authors introduced the ETC value, as in "Easy to Change". That made me think of ["A Philosophy of Software Design"](/2023/10/19/reading-notes-philosophy-software-design/#complexity-in-software).

### "Forgo following fads"

That nice alliteration reminded me of ["Kill it with fire"](/2023/11/20/reading-notes-kill-it-with-fire-marianne-bellotti/).

### Reversibility

The paragraphs on reversibility, i.e. flexible architectures, make me feel like sharing this excellent blog post by Vicki Boykis: ["Commit to your lock-in"](https://vickiboykis.com/2019/02/10/commit-to-your-lock-in/) that I often think about, and that's sort of about not thinking too much about reversibility. :sweat_smile:

### Encouragement to learn more about the shell

I should learn more shell commands. Incidentally, after reading about that in the book, I had to add a [`system2()`](https://ropensci.org/blog/2021/09/13/system-calls-r-package/) command to a script of mine.

### "Achieve Editor Fluency"

Also a good piece of advice. Have you read Nick Tierney's great post ["Get Good with R: Typing Skills and Shortcuts"](https://www.njtierney.com/post/2023/12/04/get-good-type-fast/)? On the other hand I'm also reminded me of the video ["What editor do you use"](https://www.youtube.com/watch?v=dIjKJjzRX_E)? :grin:

### Engineering daybook

The authors encourage the practice of writing down things you learn, challenges, every day. I most often have a piece of paper near me, including my sticky note for my posts about [useful functions](/tags/useful-functions/), but haven't been able to really stick to maintaining an engineering daybook. Since I work from home I don't observe many people at work, so have to resort to tech books to imagine people working :wink:, and know that the protagonist of [The Unicorn Project](https://itrevolution.com/product/the-unicorn-project/) maintains one.[^1]

### "Act locally"

The occasion for me to spread the word about the existence of the fantastic [withr package](https://withr.r-lib.org/) for locally changing options, environment variables, languages, etc.; for instance in examples and tests.

### Property-based testing

Do you know about Mark Padgham's [autotest package](https://docs.ropensci.org/autotest/)? Using it would allow one to try out property-based testing in one's package, by feeding random input into functions. The book says one is often surprised by the results of property-based testing. :sweat_smile:

### "Build a Test Window"

I remember similar advice in Jenny Bryan's fantastic talk ["Object of type 'closure' is not subsettable"](https://www.youtube.com/watch?v=vgYS-F8opgE) ([slide 77](https://speakerdeck.com/jennybc/object-of-type-closure-is-not-subsettable?slide=77)).

### "Delight your Users"

The link I'll throw here is François Chollet's post ["User experience design for APIs"](https://blog.keras.io/user-experience-design-for-apis.html).

### A last part on ethics

The book's second edition ends with some thoughts on responsability, with the tip "Don't enable scumbags".

## Conclusion

I have ambivalent thoughts about The Pragmatic Programmer: it's an extensive reference for programmers, but not my favorite tech book. Have you read it? Do you know other similar reference books? Are you planning to write one and do you need readers of advanced copies? :smile_cat:

[^1]: Have you ever read riveting kid books about some little character "who don't want to share", "who bakes a cake", or "who discovers daycare"? The Unicorn Project, and The Phoenix Project are similar books, but for adults working with tech.

