---
layout: post
title: "Pets or livestock? Naming your RMarkdown chunks"
comments: true
---


Today I made a [confession on Twitter](https://twitter.com/ma_salmon/status/894877595417948160): I told the world I had spent my whole career not naming chunks in RMarkdown documents. Even if I had said one should name them when teaching RMarkdown. But it was also a tweet for showing off since I was working on the first manuscript with named chunks and loving it. I got some interesting reactions to my tweet, including [one that made me feel better about myself](https://twitter.com/thomasp85/status/894882639303368705) (sorry Thomas), and other ones that made me feel like phrasing why one should name RMarkdown chunks.

[Hadley Wickham asked](https://twitter.com/hadleywickham/status/894889893922459648) whether chunks were pets or livestock, as in [his analogy for models](https://twitter.com/mikeksmith/status/857583637465878528). Livestock chunks are identified by numbers, not names, in the case of chunks defined by position. I now think we have good reason to consider them as pets and here's why...

<!--more-->

![](/figure/source/2017-08-08-chunkpets/running.png)

_My fur sibling does come if you call him_

* Navigating the RMarkdown document. You can yell a dog's name and it'll come to you. Well hopefully... In any case in RStudio you can actually navigate named chunks fairly easily. 

* Making your RMarkdown easier to understand. Even if you comment code, having an informative code chunk name will help your collaborators when they read your file. For that, the chunk names should reflect what's being done in them, not your creativity, keep that for your real pets or kids. Naming the chunks has moreover made me divide my code into chunks doing one thing, e.g. preparing the data, then fitting a model. David Robinson [suggested](https://twitter.com/drob/status/738786604731490304) naming chunks such as to make dependencies more explicit, and the thread contains some nice tips about automatic dependencies between chunks, something I need to explore more myself (thanks [Nick](https://twitter.com/nj_tierney) for providing me with this tweet link!). [Don't use spaces or dots in chunk labels](https://yihui.name/knitr/options/#chunk-options), and Nick even says not to use "_" but rather "-" because it makes names navigation easier at least on Mac.

![](/figure/source/2017-08-08-chunkpets/chunkdown.png)

_Chunk down! Don't worry, Mowgli was just taking a nap._

* Reading error reports or progress of knitting. It's much easier to find which chunk exactly is down, or to see how well knitting is going, with chunk names.

* Moving chunks around, [adding some](https://twitter.com/robjhyndman/status/894886426885578752). In particular, cache results are saved by chunk name or position, so caching is more efficient with named chunks. With unnamed chunks you might end up unwillingly re-running that long slow chunk!

![](/figure/source/2017-08-08-chunkpets/cool.png)

_When you're cool and you know it_

* [Feeling organized](https://twitter.com/pallavipnt/status/894894067179335681) (no shame, Pallavi!) and empowered. And boasting on Twitter.

Truth be told maybe I won't always name my chunks, e.g. in blog posts, but for manuscripts and other bigger projects I think it's the way to go. And now I need to go read more about `autodep` in `knitr`, at least to distract myself for missing my fur sibling too much!

