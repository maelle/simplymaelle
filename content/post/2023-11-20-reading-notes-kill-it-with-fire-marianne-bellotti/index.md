---
title: "Reading notes on Kill It with Fire by Marianne Bellotti"
date: '2023-11-20'
slug: reading-notes-kill-it-with-fire-marianne-bellotti
output: hugodown::hugo_document
tags:
  - good practice
  - books
rmd_hash: 4a9c9f43e80e7036

---

Another month, another long train trip enjoyed in the company of great books, among which a work-related one, [Kill it with fire by Marianne Bellotti](https://nostarch.com/kill-it-fire). As indicated by its subtitle "Manage Aging Computer Systems (and Future Proof Modern Ones)", the book deals with handling legacy computer systems. The focus is on bigger projects (including serious internal politics) than what I usually deal with, but there's some valuable lessons for any project size in there.

It was actually my second reading of the book, since I regretted not having taken notes the first time around! Some aspects had already stuck with me though.

Let's dive into some highlights...

## Dealing with legacy code is a honour

The number one thing I got out of this book is an enhanced appreciation for having to work with legacy codebases. I did not start out hating them, but still, I'm glad to now be able to refer to these two quotes for instance (although I do not work with *very* old code!):

> "legacy technology exists only if it is successful. These old programs are perhaps less efficient than they were before, but technology that isn't used doesn't survive decades."

And

> "To most software engineers, legacy systems may seem like torturous dead-end work, but the reality is systems that are not used get turned off."

## Why are older systems bad

Chapter 3 ("Evaluating Your Architecture") Marianne Bellotti explains three kinds of issues that might be the reason a modernization is wished for:

-   **tech debt**. It reminded me, among other things, of John Ousterhout's ["tactical vs strategic programming"](/2023/10/19/reading-notes-philosophy-software-design/#tactical-vs-strategic-programming);
-   **performance issues**;
-   **stability issues** (surprising failures, due to tight coupling and/or complexity).

In Chapter 10 ("Future Proofing") the author mentions two ways in which systems age:

-   **change of usage patterns** (scale);
-   the **resources that back them** deteriorate.

That's the chapter where the idea of **legacy modernization being an anti-pattern** is presented: if you maintain your system well over time, you won't need a special operation. :sweat_smile:

## How people adopt new software

There's a lot of content about how technology gets adopted or not, including these two nuggets of wisdom:

-   "familiar interfaces help speed up adoption" (it reminded me of Quarto vs R Markdown);
-   "people gain awareness of interfaces and technology through their networks, not necessarily by popularity".

The latter is not the only time that the book underlines human aspects of computer systems, far from it.

## Lessons for planning modernization efforts

Obviously the book is full of lessons about planning modernization efforts, but here are three of them:

**"Migrating for Value, Not for Trends"**. Don't go for the next new shiny thing, pragmatically assess whether it'll be best for the system (or just best for bragging about it to your peers :sweat_smile: See the next section of this post!)

It's important to first learn how to **understand**, **test** (what does not fail?), **monitor** (what fails?) the codebase before one makes big changes to it.

The book includes many tips on **how to build and keep momentum of a team or organization** during a modernization effort (the whole chapter 5, "Building and Protecting Momentum"), as well as how to keep one's own motivation (marking time, or I guess more generally tracking progress).

## The workplace/career related aspects of code

At several points, Marianne Bellotti underlines how the atmosphere at an organization will influence its code. For instance if there's no well-defined career ladder at an organization, maybe people will make choices to get recognition from their peers outside of it, for instance by being the first to try out tool X for some application.

This quote would pair well with some more reading on psychological safety and the like, as that's what it made me think of:

> "Good technologists should focus on what brings the most benefit and highest probability of success to the table at the current moment, with the confidence of knowing they have nothing to prove."

There's also a few pages on how people get seen, and how that influences what gets built.[^1]

## Use the codebase and its context but not too much

In my own words,

-   Retrieve context as much as possible.
-   Think of the tools available at the time the system was created as that might you help understand some constraints.
-   Respect that the system is working (otherwise it wouldn't be used any more).
-   However don't make the mistake of assuming all decisions were good ones.

## The tools are not the most important thing

Marianne Bellotti encourages the reader to be realistic about what automatic tools (for instance for traducing code from one language to another) will give you.

> "The tools themselves are not as important as the phases of excavating, understanding, documenting, and ultimately rewriting and replacing legacy systems. Tools will come and go."

## Turn things off and see what happens

I love this idea, apparently called "chaos testing", for when someone encounters a thing one isn't sure is used at all or where. You turn it off and wait for someone to complain. It's in a book so it's a legit strategy. :smile_cat:

## Tips on designing technical conversations

I don't want to reproduce what's in the book here but just to remember there are good tips around conversations in this book with exercises such as "The Saboteur".

## Breaking changes and users' procrastination

In "Communicating Failure", in Chapter 8 ("Breaking Changes"), Marianne Bellotti explains that giving too much time to users to stop using a thing to be deprecated, just leads them to procrastinate more.

## Conclusion

"Kill It with Fire" is a great book! I really enjoyed the pragmatism and the focus on human aspects of modernization.

I might re-read it when the stories of bigger projects have more value to me than being interesting stories, that is to say, if I ever participate in bigger modernization efforts!

Have you read the book? Any thoughts on it or on the general topic of modernizing old computer systems?

[^1]: These are strong pages despite citing [work by Dan Ariely](https://en.wikipedia.org/wiki/Dan_Ariely#Manipulated_data_in_an_experiment_about_dishonesty).

