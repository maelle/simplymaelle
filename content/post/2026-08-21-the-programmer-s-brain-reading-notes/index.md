---
title: "Reading notes on The Programmer's Brain by Felienne Hermans"
date: '2026-08-21'
slug: the-programmer-s-brain-reading-notes
output: hugodown::hugo_document
tags:
  - good practice
  - books
rmd_hash: a9f74fe2e374d2a9

---

Prompted (:wink:) by some AI dread, I decided to go back to some basics and re-read [The Programmer's Brain by Felienne Hermans](https://www.manning.com/books/the-programmers-brain). Felienne Hermans' work caught my attention when she gave a [keynote talk](https://resources.rstudio.com/resources/rstudioconf-2019/explicit-direct-instruction-in-programming-education/) at a Posit conference years ago. The book was a highlight of my week: extremely interesting, and easy to follow. Here are some notes, thanks to tiny bookmarks I added as a I read.

## The main characters

The main characters in the book are

- the long-term memory (knowledge);
- the short-term memory (information right now);
- the working memory (processing power).

Everything is brought back to them.

## Making an effort pays off

I am fascinated by the fact that a schoolteacher called Ballard found out that "when you actively try to recall information without additional study, you will remember more of what you learned".

You can also strengthen your memories by actively thinking, *elaborating* around something.

## Cognitive loads

Felienne Hermans summarizes the different types of cognitive load:

> Intrinsic load: how complex the problem is in itself. Extraneous load: what outside distractions add to the problem. Germane load: cognitive load created by having to store your thought to long-term memory.

Regarding extraneous load, an example that's given is a poorly formulated math problem.

## Cognitive refactoring

I remembered this idea from my first read: you can refactor code to understand it better, a refactor you do only for yourself.

One example that's given is replacing unfamiliar language constructs such as anonymous functions. It made me think of my overcomplicating a PR by both changing something crucial and replacing for loops with [reduce](/2023/07/26/reduce/), that were unfamiliar to my collaborator. I should have split the two changes in two PRs.

A related quote from the book:

> '"readable" is really in the eye of the beholder'

## Help your working memory

When mentioning strategies for helping your working memory, such as creating state tables or diagrams, the author mentioned PythonTutor by Philip Guo, which visualizes the execution of a program. It reminded me of the boomer R package by my cynkra colleague Antoine Fabri, that lets you inspect the intermediate steps of a call.

<div class="highlight">

<pre class='chroma'><code class='language-r' data-lang='r'><span><span class='nf'><a href='https://rdrr.io/r/base/subset.html'>subset</a></span><span class='o'>(</span><span class='nf'><a href='https://rdrr.io/r/utils/head.html'>head</a></span><span class='o'>(</span><span class='nv'>penguins</span>, <span class='m'>2</span><span class='o'>)</span>, <span class='nv'>bill_len</span> <span class='o'>&gt;</span> <span class='m'>47</span><span class='o'>)</span> <span class='o'>|&gt;</span> <span class='nf'>boomer</span><span class='nf'>::</span><span class='nf'><a href='https://moodymudskipper.github.io/boomer/reference/boom.html'>boom</a></span><span class='o'>(</span><span class='o'>)</span></span>
<span><span class='c'>#&gt; 💣 <span style='color: #00BBBB;'>subset(head(penguins, 2), bill_len &gt; 47)</span> </span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span>💣 💥 <span style='color: #00BBBB;'>head(penguins, 2)</span> </span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span>  species    island bill_len bill_dep flipper_len body_mass    sex year</span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span>1  Adelie Torgersen     39.1     18.7         181      3750   male 2007</span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span>2  Adelie Torgersen     39.5     17.4         186      3800 female 2007</span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span></span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span>💣 💥 <span style='color: #00BBBB;'>bill_len &gt; 47</span> </span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span>[1] FALSE FALSE</span></span>
<span><span class='c'>#&gt; <span style='color: #BBBB00;'>· </span></span></span>
<span><span class='c'>#&gt; 💥 <span style='color: #00BBBB;'>subset(head(penguins, 2), bill_len &gt; 47)</span> </span></span>
<span><span class='c'>#&gt; [1] species     island      bill_len    bill_dep    flipper_len body_mass   sex         year       </span></span>
<span><span class='c'>#&gt; &lt;0 rows&gt; (or 0-length row.names)</span></span>
<span><span class='c'>#&gt; </span></span>
<span></span><span><span class='c'>#&gt; [1] species     island      bill_len    bill_dep    flipper_len body_mass   sex         year       </span></span>
<span><span class='c'>#&gt; &lt;0 rows&gt; (or 0-length row.names)</span></span>
<span></span></code></pre>

</div>

Coupling that with [constructive](https://cynkra.github.io/constructive/), another package of Antoine's, might help one represent code better.

## Roles of variables

The book has a list (by Jorma Sajaniemi) of the eleven roles a variable can have, e.g. "fixed value" or "stepper" (i in a for loop). Interesting vocabulary! The book even features a flowchart to help us determine a role a variable plays.

## Parallels with natural languages

The author explains a technique for understanding code by circling all variables, linking them, etc. It reminds me of how I'd handle Latin text I had to translate in high school. I had a color and shape code, it looked very pretty and worked well.

Speaking of languages, the book draws some parallels between computer and natural languages. In particular, it explains how text comprehension strategies (like questioning or summarizing) apply to code reading.

## Keeping notes

I will try to do better at taking notes on a piece of paper when I work. I already do in some cases, for instance when reviewing packages for rOpenSci. But the book really insists how it can support your memory, or resume work after an interruptions.

Beside those throwaway notes, it's important to document/comment code to prevent future contributors to fall in some traps and to facilitate onboarding of new contributors. [Recent example](https://github.com/duckdb/duckdb-r/tree/main/handbook).

## Further programming languages

IDEs:

> "transfer between two programming languages is more likely if you program two different languages in the same IDE, which is a strong argument for using one IDE for multiple languages."

Language choice:

> "if you set out to learn a new language to expand your way of thinking, it's important to pick one language that's fundamentally different from the ones you've already mastered."

The book also explains how some knowledge you have in one language means you might have to "unlearn" some syntaxes. It reminded of *faux amis* (false friends) for French-speaking learners of English, like "actually" that doesn't mean *actuellement* (currently).

## Names are important...

And the book explains why, gives useful tips. There's a whole chapter on the topic.

I liked one of the conventions by Butler: "Identifiers should consist of words and only use abbreviations when they are more commonly used than the full words". Recently I was very stubborn about not using "comb" for "combination" in igraph. I also enjoyed another conventions from that same list: "Identifier should not combine uppercase and lowercase character in non standard ways", with the example `Page_counter`. I might be selectively reading the rules that I like. :innocent:

The chapter conveys the perspective by Allamanis that names should be consistent across a codebase, because that helps chunking (when you parse code into meaningful bits).

The author underlines that you should evaluate the quality of names after coding, not during code, as it might be too much cognitive load. It made me think of Git commits: you can [improve them after coding](/talks/2025-11-24-git-history/), when you're coding you might not be able to create a perfect Git history.

Another tidbit that I found interesting is that when you improve names in your codebase, the places where you find bad names might be the places with hidden bugs for various reasons (like correlation between bad names and mistakes by a novice programmer or a programmer confused by the complexity of the problem at hand).

## Automatization

Some things you know so well that you can do them without thinking much, which makes you more efficient. An argument for learning and deliberate practice.

## Reading about code

Sometimes if you're writing very complex code, you don't learn much, "your brain was so engaged it could not store the solutions".

Therefore, worked examples can help you learn: collaborating with someone, reading code on GitHub, reading books or blog posts about code. You don't only (and necessarily) learn by doing.

## Curse of expertise

I enjoyed reading again about the "curse of expertise", that is especially relevant when teaching:

> "Once you have mastered a skill sufficiently, you will inevitably forget how hard it was to learn that skill or knowledge."

## Conclusion

I would highly recommend reading The Programmer's Brain by Felienne Hermans! Maybe even more than once like I did since I had clearly not committed everything to long-term memory. :grin:

The epilogue mentions some further reading including [A Philosophy of Software Design by John Ousterhout](/2023/10/19/reading-notes-philosophy-software-design/) which solved a mystery for me: *that* is where I had heard of that book!

