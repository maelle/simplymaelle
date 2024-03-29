---
title: "Reading notes on A Philosophy of Software Design by John Ousterhout"
date: '2023-10-19'
slug: reading-notes-philosophy-software-design
output: hugodown::hugo_document
tags:
  - good practice
  - books
rmd_hash: b13652d03e297b61

---

When I see a book recommendation somewhere, be it for work or leisure, I often either order the book or set an alert in my [favorite online second-hand bookstore](https://www.momox-shop.fr/). By the time I am notified the book is available, I sometimes don't remember why I listed it! That's what happened for the book A Philosophy of Software Design by John Ousterhout. I decided to trust my past self and buy it. Unfortunately I got the first not the [second edition](https://web.stanford.edu/~ouster/cgi-bin/book.php).

For a change I wrote some reading notes, reflecting what resonated more with me at this stage in my software development journey.

## First and later impressions

I found the title of the book misleading: the word "philosophy" made it sound unapproachable, but it was approachable, and also rather short (about 169 pages, including examples no one will know you skipped[^1]).

I got a copy that was annotated. Sadly the notes didn't provide any novel insight, but the handwriting is good and the notes repeat what's important, perfect for future skimming.

I appreciated the tone of the book which wasn't too dogmatic compared to [other sources](https://qntm.org/clean) out there.

## Complexity in software

The most important concept is the book is **complexity**, what it is and how to avoid it. The author defined three manifestations of complexity:

-   Change amplification, meaning it's hard to change something. It reminds me of this quote I heard from Kara Woo's posit keynote talk: ["If you can't make changes because you are afraid of breaking something, it's already broken."](https://speakerdeck.com/karawoo/r-not-only-in-production?slide=76).
-   Cognitive load, at which point I have to tell you to read [The Programmer's Brain by Felienne Hermans](https://www.manning.com/books/the-programmers-brain). I think about it regularly and see my R friends mentioning it.
-   Unknown unknowns. 👻

## Priority to the interface

A point made at least twice in the book is that you should give priority to simplifying the interface of things not their implementation.

> "When developing a module, look for opportunities to take a little bit of extra suffering upon yourself to reduce the suffering of your users".

This is also present in the author's idea of "Define errors out of existence".

This made me think about [retries in HTTP requests](https://blog.r-hub.io/2020/04/07/retry-wheel/). When writing a function interfacing an API I could simply return the error, forcing the user to handle the error themselves, or I could add retries, that would potentially make all errors disappear. It's not a perfect example since retries aren't hard to add with say httr2 or httr, but that was what I was thinking of.

Another point about the interface is that you only split two things if it simplifies the interface. That's a better way to think about it than just thinking in number of lines.

## Tactical vs strategic programming

The author compares two approaches to programming:

-   **Tactical programming** is where you squash bugs and add features while racking up tech debt, not thinking about software design.

-   **Strategic programming** is where you take more time to implement things so that the software design remains good or even becomes better over time.

Unsurprisingly the author defends the second approach, explaining how it pays off. The author does acknowledge it might be hard to use the second approach depending on, for instance, [deadlines](https://enpiar.com/2023/05/30/obey-the-timer/).

Strategic programming, and the author's insistent advice to "stay strategic" reminded me of a [paragraph in the Tidyverse code review guide](https://code-review.tidyverse.org/reviewer/purpose.html):

> "On the other hand, it is the duty of the reviewer to make sure that each PR is of such a quality that the overall code health of the package is not decreasing as time goes on. This can be tricky, because codebases often degrade through small decreases in code health over time. This isn't necessarily anyone's fault. It is a natural process resulting from the accumulation of technical debt over time, which must continually be fought against."

## Comments

> "Good comments can make a big difference in the overall quality of a system."

I appreciated seeing a few chapters about comments. I wrote a [post on comments](https://blog.r-hub.io/2023/01/26/code-comments-self-explaining-code/) myself earlier this year.

The author write that comments belong in the code, not the commit log, which is true but with the [fledge package](https://fledge.cynkra.com/dev/) you can transform commit messages into changelog content. Furthermore, I feel that Git blame can be useful no matter how good your comments are.

The book mentions that if a piece of code is hard to comment maybe you should refactor it. I like how it's similar to another idea in the book: if a variable is hard to name maybe it indicates some weakness of the code design.

## Variable names

Apart from the idea I've just mentioned, one piece of advice that is reported in the book resonated with me. It's a comment by Andrew Gerrand: "The greater the distance between a name's declaration and its uses, the longer the name should be".

## Consistency

The author underlines the importance of consistency (in names, coding style, design patterns...), that makes your codebase easier to grasp and contribute to. To ensure consistency you should document it which made me think of [contributing guides](https://devdevguide.netlify.app/maintenance_collaboration#contributing-guide), and/or use automatic tools, which made me think of the [lintr package](https://lintr.r-lib.org/reference/index.html).

## Conclusion

I am glad I ordered this book past me had bookmarked! I found it was very readable, with some good advice and ways to express it. I am pretty sure I'll skim it in the future. My first recommendation of resources about good code would remain [The Art of Readable Code by Dustin Boswell and Trevor Foucher](https://www.oreilly.com/library/view/the-art-of/9781449318482/), [The Programmer's Brain by Felienne Hermans](https://www.manning.com/books/the-programmers-brain), [Jenny Bryan's talk "Code Smells and Feels"](https://github.com/jennybc/code-smells-and-feels) and [Hadley Wickham's Tidy Design newsletter that goes with a book-in-progress](https://tidydesign.substack.com/).

If you read the book, what were your take-aways? What is your favorite resource to learn how to program better?

## Other reviews of / notes on the book

-   <https://johz.bearblog.dev/book-review-philosophy-software-design/>
-   <https://blog.pragmaticengineer.com/a-philosophy-of-software-design-review/>

[^1]: Unless you blog about it.

