---
title: 'Storrrify #satRdayCapeTown 2018'
date: '2018-03-24'
slug: satrdaycapetown
comments: yes
---


One week ago I was in Cape Town for the [local satRday conference](https://capetown2017.satrdays.org/), where I had the honor to be one of the two keynote speakers, the other one being sports analytics extraordinaire [Stephanie Kovalchik](http://on-the-t.com/) (You can read Stephanie Kovalchik's account of the conference in [this blog post](http://on-the-t.com/2018/03/16/satrday-capetown/)). It was a fantastic experience! The event was very well organized, and 100% corresponds to its description as a "one day conference packed with R goodness". You can watch all talks [on Youtube](https://www.youtube.com/watch?v=NiMqIMKY3RA&list=PLQPtslMzGu4oDr6hjKm1aQ2XN7P35vCbk). In [my talk](https://www.youtube.com/watch?v=lZ3deq52qCk), I presented [rOpenSci onboarding system of packages](https://github.com/ropensci/onboarding/) and... wore a hard hat! 

It'd be a bit hard for me to really write a good recap of satRday that'd do it justice! Instead, I'll use `rtweet` and a bit of html hacking to storrrify it (like [Storify](https://storify.com/), but in R) using my live tweets!

<!--more-->

# Getting my satRday tweets

To get my own satRday Cape Town tweets, I'll first get my timeline and then filter tweets containing the official conference hashtag, ["#SatRdayCapeTown"](https://twitter.com/search?q=%23satrdaycapetown&src=tyah). I shall only keep _my_ tweets.


```r
my_tweets <- rtweet::get_timeline(user = "ma_salmon", n = 5000)
my_satrday_tweets <- dplyr::filter(my_tweets,
                                   stringr::str_detect(text, "[sS]at[rR]day[cC]ape[tT]own"),
                                   !is_retweet)
my_satrday_tweets <- dplyr::arrange(my_satrday_tweets, created_at)
```

I got 54 tweets. 

# Embedding tweets

I wanted to _embed_ tweets, not to `rtweet::tweet_shot` them, because I wanted to display clickable links. I used the Twitter API embed endpoint after [reading this webpage](https://dev.twitter.com/web/embedded-tweets). This endpoint isn't currently included in `rtweet`.


```r
library("magrittr")
embed_tweet <- function(tweet_id){
  url <-glue::glue("https://twitter.com/ma_salmon/status/{tweet_id}") 
  
  httr::GET("https://publish.twitter.com/oembed",
            query = list(url = url,
                         hide_thread = "true")) %>%
    httr::content() %>%
    .$html
}
```

I'll now write all html chunks in an html file.



```r
fs::file_create("satrdaycapetown.html")
embed_and_save <- function(tweet_id){
  embed_tweet(tweet_id) 
}

purrr::map_chr(my_satrday_tweets$status_id, embed_tweet) %>%
  paste() %>%
  writeLines("satrdaycapetown.html", useBytes = TRUE)
```

# And voilà!

I'll include all tweets below (via a [Jekyll include](https://stackoverflow.com/questions/28030858/jekyll-include-html-partial-inside-markdown-file), with the html living in _includes, this is probably very hacky...), happy reading! Thanks again to the organizers, participants and sponsors of SatRday Cape Town for making it such an enjoyable experience!

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">&quot;GitHub is a treasure trove of public activity just waiting to be analyzed&quot; YES! More on my own 💙 for GH data at <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> in a few days! 🛫 <a href="https://t.co/O7n4EIqpbj">https://t.co/O7n4EIqpbj</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/973532076908695552?ref_src=twsrc%5Etfw">March 13, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">💯 Things are getting real down here! <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> <a href="https://t.co/V74HhcVZ9c">https://t.co/V74HhcVZ9c</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974011430264606722?ref_src=twsrc%5Etfw">March 14, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">A good way to get familiar with my accent, dear <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> attendees 😉 poke <a href="https://twitter.com/DataWookie?ref_src=twsrc%5Etfw">@DataWookie</a> <a href="https://t.co/HOGoWIqF6I">https://t.co/HOGoWIqF6I</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974207625465344000?ref_src=twsrc%5Etfw">March 15, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/theoni_p?ref_src=twsrc%5Etfw">@theoni_p</a> wrote a legend for the sticky notes at my <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> package dev workshop! (<a href="https://twitter.com/thecarpentries?ref_src=twsrc%5Etfw">@thecarpentries</a> style!) 👩
🎨<br><br>The nearby <a href="https://twitter.com/thecarpentries?ref_src=twsrc%5Etfw">@thecarpentries</a> workshop uses pink for &quot;confused&quot; so a helper was scared when she came in &amp; saw all the pink notes in our room 😂 <a href="https://t.co/H0ubBpZYNF">pic.twitter.com/H0ubBpZYNF</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974606612773163008?ref_src=twsrc%5Etfw">March 16, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Now I exactly know what to improve next time I teach <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> package development in case someone wants me to do that again! 😀<br><br>Thanks <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> participants for a great day and insightful feedback! 🚦 <a href="https://t.co/l6h6GCYvyx">pic.twitter.com/l6h6GCYvyx</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974661418850058240?ref_src=twsrc%5Etfw">March 16, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">And thanks a lot to the helpers, <a href="https://twitter.com/jonmcalder?ref_src=twsrc%5Etfw">@jonmcalder</a> &amp;co who made everything run smoothly! 🌟🌟🌟 <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/mxVGpK8oqn">https://t.co/mxVGpK8oqn</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974675797054316544?ref_src=twsrc%5Etfw">March 16, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Material for my <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> 📦 development workshop <a href="https://t.co/lGnaBXaHYy">https://t.co/lGnaBXaHYy</a><br><br>Slides <a href="https://t.co/lZmUHnWzVv">https://t.co/lZmUHnWzVv</a> <a href="https://t.co/YuYse37zo6">pic.twitter.com/YuYse37zo6</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974683971467476992?ref_src=twsrc%5Etfw">March 16, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Roberto Benetto opening <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> with an overview of <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> sf! 🌐🗺 And a pun that made everyone laugh 👏 (wait for the recording to hear it 😜) <a href="https://t.co/lxRoQkDKMR">pic.twitter.com/lxRoQkDKMR</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974901803383951360?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">He&#39;s a fan of reading GitHub issues of <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> packages to see what problems and ideas people have. So am I! 🕵️
♂️🕵️
♀️ <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974903554489143296?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">⚡talks time at <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a>! 🏃
♂️🏃
♀️</p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974906278131654656?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Christopher Waspe about the flowcar package. Nothing to do with 🚗! It&#39;s about ecological networks! (No URL found/given sorry 😢) ⚡ <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974907833446752256?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr"><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> speakers please mention your Twitter handle if you have one &amp; don&#39;t have it on your title slide 😉</p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974908340424839168?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Vishalin Pillay about automatic report generation! RMarkdown 💙💚💛 <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974908691903283200?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Maphale Matlala about testing the IUCN Red List 🚦 for Ecosystems methodology on 🇿🇦 datasets <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> using the redlistr <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> package <a href="https://t.co/e8QhgwEcKd">https://t.co/e8QhgwEcKd</a> <a href="https://t.co/yI18oNhUyg">pic.twitter.com/yI18oNhUyg</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974910580531318784?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Slides for my <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> talk about the <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> onboarding process of <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> packages 📦<a href="https://t.co/OOPepRT2G3">https://t.co/OOPepRT2G3</a><br><br>Poke co-editors <a href="https://twitter.com/_inundata?ref_src=twsrc%5Etfw">@_inundata</a> <a href="https://twitter.com/noamross?ref_src=twsrc%5Etfw">@noamross</a> <a href="https://twitter.com/sckottie?ref_src=twsrc%5Etfw">@sckottie</a> <br><br>And here are the faces of onboarded packages! <a href="https://t.co/TAXqxr21A1">pic.twitter.com/TAXqxr21A1</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974930652050182145?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/painblogR?ref_src=twsrc%5Etfw">@painblogR</a> is showing us how to make data analysis purrr with 🥕🥕🥕 data as an example <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/sCeRMFcfCd">pic.twitter.com/sCeRMFcfCd</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974934793858232320?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">.<a href="https://twitter.com/painblogR?ref_src=twsrc%5Etfw">@painblogR</a> &#39;s code <a href="https://t.co/23C6Puqxm8">https://t.co/23C6Puqxm8</a><br><br>And 🥕🥕🥕 dataset <a href="https://t.co/CiwKJks3im">https://t.co/CiwKJks3im</a><br><br>A really neat intro to purrr, including pros and cons (nesting hell anecdote 😂) 👌<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974938130485792768?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Sean Soutar showing us how to gather data from dynamic web forms with Docker &amp; the <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> RSelenium <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> package <a href="https://t.co/WdTgaSzJZe">https://t.co/WdTgaSzJZe</a> ⭐ <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974939668579082246?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Futurology! (extreme risk prediction) with Alice Elizabeth Coyne 📈🔮 <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/2G3HPOxT1g">pic.twitter.com/2G3HPOxT1g</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974941369616740353?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Super <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> organizer-in-chief <a href="https://twitter.com/DataWookie?ref_src=twsrc%5Etfw">@DataWookie</a> speaking about productivity hacks, we&#39;d better all take notes! 🗒✏</p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974942461746442242?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">.<a href="https://twitter.com/DataWookie?ref_src=twsrc%5Etfw">@DataWookie</a> productivy hacks<br>✅ configure ahead <br>✅ ditch RStudio 😱 &amp; use the shell/R command line instead (now I want to debate functions&amp;pkgs vs parameterized scripts with him 😁🤺)<br>✅ use the ☁️<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974946034802679809?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Now listening to <a href="https://twitter.com/realseem?ref_src=twsrc%5Etfw">@realseem</a> about his journey from Python🐍 to R, with game making as motivator 🎮<br><br>Very entertaining and enlightening lightning talk! <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/pl7QaNSNzx">pic.twitter.com/pl7QaNSNzx</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974948780180230145?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Schalk Heunis about using R for racing! <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <br>Using R to train the ConvNet of a self-driving RC 🚗<br><br>Find his project at <a href="https://t.co/eyPS2gLKnL">https://t.co/eyPS2gLKnL</a><br><br>He mentioned<br>⭐ <a href="https://t.co/yzhQX9qvSe">https://t.co/yzhQX9qvSe</a><br>⭐ <a href="https://t.co/M6xzQOX4rC">https://t.co/M6xzQOX4rC</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974950699275612160?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Neil Rankin says satRday 2017 changed his life, he discovered useful 🛠! He e.g. mentions <a href="https://twitter.com/JennyBryan?ref_src=twsrc%5Etfw">@JennyBryan</a> &#39;s <a href="https://t.co/G8vxGml00t">https://t.co/G8vxGml00t</a><br><br>He&#39;s now talking about identifying bias in 🇿🇦 graduate recruitment<br><br> <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974973634279337984?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Such a fascinating talk about bias. 😮<br><br>Neil Rankin used tidy text analysis to analyze personal biographies by self assigned race (thanks to <a href="https://twitter.com/juliasilge?ref_src=twsrc%5Etfw">@juliasilge</a> &#39;s presence last year).<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974976465644261376?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Jed Stephens walked us through some scary factors data 😉 <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974979382967242752?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Deveshnie Mudaly, SQL wiz &amp;engaging speaker, uses a cool example in her talk about using SQL from <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> ☄<br><br>PSA! She&#39;s looking for collaborators on a Harry Potter R package! Find her at <a href="https://t.co/oUJZ1unLH0">https://t.co/oUJZ1unLH0</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/1pAjcC3zRC">pic.twitter.com/1pAjcC3zRC</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974980960432345089?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Unsurprisingly <a href="https://twitter.com/rugbystatsguy?ref_src=twsrc%5Etfw">@rugbystatsguy</a> is presenting about 🏈! Either put your Twitter handle on your title slide or... have one people can guess 😉<br><br>He mentioned the <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> plotly package <a href="https://t.co/zKD6YuxRv5">https://t.co/zKD6YuxRv5</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974982467273752576?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Time for <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> &#39;s keynote at <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> ! 🎾 <a href="https://t.co/qPDQywiOPr">pic.twitter.com/qPDQywiOPr</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974982892781793281?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">.<a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> works for GIG that will revolutionize 🎾 through data science<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974985754345660416?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">If you are into 🎾 &amp; <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> check out <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> &#39;s impressive package produced by her collecting tons of data from the web!<a href="https://t.co/mueGjGXyVc">https://t.co/mueGjGXyVc</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974986149822427137?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">This shows <br><br>✅ numbers calculated by <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a>&#39;s group (win probability computed before and during events!)<br><br>✅ one of the reasons she 💙s her job!<br> <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/iDwrnGBGfF">pic.twitter.com/iDwrnGBGfF</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974987597977092097?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Now we&#39;ll get to see how <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> &#39;s group gets there 👌<br><br>📽🔩<a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> ☁️🗃<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/quQaUmES2E">pic.twitter.com/quQaUmES2E</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974988810038767621?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Live demo by <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> 😎 Here is my own metrics XML 😉<br><br>&lt;talk&gt;<br>  &lt;content&gt;fascinating&lt;/content&gt;<br>  &lt;speaking&gt;extremely engaging&lt;/speaking&gt;<br>&lt;/talk&gt;<a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974992497406595072?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">.<a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> tips for <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> software for real time<br><br>🎾 Think modularly<br><br>🎾 Profile bottlenecks using lineprof <a href="https://t.co/L8zfOoN7aX">https://t.co/L8zfOoN7aX</a><br><br>🎾Embrace the <a href="https://twitter.com/hashtag/tidyverse?src=hash&amp;ref_src=twsrc%5Etfw">#tidyverse</a><br><br>🎾Further optimize with e.g. datatable, parallel/foreach/other parallel processing tools, Rcpp<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974994325569523714?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">.<a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> : GIG reports are generated automatically &amp; in <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> RMarkdown<br><br>She shows us an example of a parameterized report cf <a href="https://t.co/U6HoKDoIwJ">https://t.co/U6HoKDoIwJ</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/Py5S0o7RSZ">pic.twitter.com/Py5S0o7RSZ</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974996206517661696?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">.<a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a>&#39;s tips for real time <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> RMarkdown<br><br>⚾️ Doc as function<br><br>⚾️ Make your summaries and plots generalizable<br><br>⚾️ Separate content from style with a CSS style sheet<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974997120221696000?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Find the material for <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a> awesome talk here <a href="https://t.co/VvAk7wSODo">https://t.co/VvAk7wSODo</a> 💁
♀️<br><br>Pro tip, nagivate to the repo root <a href="https://t.co/qviYxENses">https://t.co/qviYxENses</a> for even more <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> goodness from her workshop 👩
🏫 and her <a href="https://twitter.com/RLadiesCapeTown?ref_src=twsrc%5Etfw">@RLadiesCapeTown</a> talk about sports blogging 💜💁
♀️<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/974999395237285890?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">It was good promotion of sf, with nice examples! The joke wasn&#39;t about sf though.<br><br>Roberto Benetto&#39;s code lives here <a href="https://t.co/IRY46TN4RP">https://t.co/IRY46TN4RP</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975004308797296643?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/UbuntR314?ref_src=twsrc%5Etfw">@UbuntR314</a> said <a href="https://twitter.com/juliasilge?ref_src=twsrc%5Etfw">@juliasilge</a>&#39;s last year inspired him to use sentiment analysis!<br><br>He developed an internal package for analysing news survey data and will soon blog about it at <a href="https://t.co/XUYYSrHQbM">https://t.co/XUYYSrHQbM</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/EWcVP3yCxT">pic.twitter.com/EWcVP3yCxT</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975009361901780992?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/naasvanheerden?ref_src=twsrc%5Etfw">@naasvanheerden</a> explains his workflow at work using R packages and Shiny apps... and says the packages will be improved thanks to my workshop yesterday 😎😊<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975010294069112832?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">In his nice talk <a href="https://twitter.com/naasvanheerden?ref_src=twsrc%5Etfw">@naasvanheerden</a> mentioned <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> coming to the rescue... ICYMI <a href="https://twitter.com/alice_data?ref_src=twsrc%5Etfw">@alice_data</a> made a real logo for that! 😉<a href="https://t.co/zCre4ZWyGG">https://t.co/zCre4ZWyGG</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/CSYdqERUA1">pic.twitter.com/CSYdqERUA1</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975010953678020609?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">The very energetic <a href="https://twitter.com/SaintlyVi?ref_src=twsrc%5Etfw">@SaintlyVi</a> 🏃
♀️ is now introducing CKAN &amp; <a href="https://twitter.com/hashtag/OpenData?src=hash&amp;ref_src=twsrc%5Etfw">#OpenData</a> portals<br><br>She mentioned the <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> ckanr <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> package <a href="https://t.co/fWLm4YNrIj">https://t.co/fWLm4YNrIj</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/GhS4Qm4zyb">pic.twitter.com/GhS4Qm4zyb</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975012813591777280?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Why did I say &quot;mentioned&quot;?! <a href="https://twitter.com/SaintlyVi?ref_src=twsrc%5Etfw">@SaintlyVi</a> has now walked us through an example of <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> ckanr use &amp; is now presenting us a typical ckanr workflow! <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/dWyzxrs5jh">pic.twitter.com/dWyzxrs5jh</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975014065205637120?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Here is <a href="https://twitter.com/SaintlyVi?ref_src=twsrc%5Etfw">@SaintlyVi</a> intro code to <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> ckanr <a href="https://t.co/QNonaMT6aJ">https://t.co/QNonaMT6aJ</a> 🏃
♀️<br><br>I also liked her promotion of <a href="https://twitter.com/RLadiesCapeTown?ref_src=twsrc%5Etfw">@RLadiesCapeTown</a>! 💜<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975014804732694529?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/OpenSourceQuant?ref_src=twsrc%5Etfw">@OpenSourceQuant</a> 📈📉 on blotter <a href="https://t.co/BMjUgWvDQW">https://t.co/BMjUgWvDQW</a><br><br>Hey it doesn&#39;t have a README nor a repo description yet 😱😜<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975017892013072384?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/pssGuy?ref_src=twsrc%5Etfw">@pssGuy</a> about creating interactive plots in 5 minutes ⏱ starring <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> 🛠<br><br>✅ the <a href="https://twitter.com/hashtag/tidyverse?src=hash&amp;ref_src=twsrc%5Etfw">#tidyverse</a><br>✅ <a href="https://twitter.com/rOpenSci?ref_src=twsrc%5Etfw">@rOpenSci</a> plotly <a href="https://t.co/zKD6YuxRv5">https://t.co/zKD6YuxRv5</a><br>✅ <a href="https://twitter.com/MilesMcBain?ref_src=twsrc%5Etfw">@MilesMcBain</a> datapasta that he tried to pronounce à la 🇦🇺 😉 <a href="https://t.co/lyjnUOcKTH">https://t.co/lyjnUOcKTH</a><a href="https://t.co/tO3ME03c9R">https://t.co/tO3ME03c9R</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/VfrNcX8RON">pic.twitter.com/VfrNcX8RON</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975019829328478208?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Last talk, by David Lubinsky, is about profiling using <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> profvis! (poke <a href="https://twitter.com/_ColinFay?ref_src=twsrc%5Etfw">@_ColinFay</a>)<a href="https://t.co/Ol2KfeBwcQ">https://t.co/Ol2KfeBwcQ</a> <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975021332957188096?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Why profile <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> code according to David Lubinsky<br><br>▶️ not wasting resources in production<br>▶️ often easy to get improvements<br>▶️ R is more than a prototyping language<br>▶️ especially useful with Shiny<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/l34yKrqML1">pic.twitter.com/l34yKrqML1</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975022195310321664?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Such an interesting, clear and enjoyable live demo of <a href="https://twitter.com/winston_chang?ref_src=twsrc%5Etfw">@winston_chang</a> &#39;s profvis package by David Lubinsky 👌<a href="https://t.co/Ol2KfeBwcQ">https://t.co/Ol2KfeBwcQ</a><a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/RSnNdihuuq">pic.twitter.com/RSnNdihuuq</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975024277757091842?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">.<a href="https://twitter.com/realseem?ref_src=twsrc%5Etfw">@realseem</a> won a book signed by <a href="https://twitter.com/hadleywickham?ref_src=twsrc%5Etfw">@hadleywickham</a> thanks to his great ⚡talk this morning! Well deserved!<a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> <a href="https://t.co/4pVymr8wDe">pic.twitter.com/4pVymr8wDe</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975028215348645888?ref_src=twsrc%5Etfw">March 17, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">Flying home today&amp;tomorrow from <a href="https://twitter.com/hashtag/SatRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#SatRdayCapeTown</a> after a great conference, thx a ton to the organizers, participants &amp;sponsors! 🌴✈⛄ <a href="https://t.co/OQWa650oF3">pic.twitter.com/OQWa650oF3</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975275576788439040?ref_src=twsrc%5Etfw">March 18, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet" data-conversation="none"><p lang="en" dir="ltr">Not sure if relevant but it seems that sports analytics extraordinaire <a href="https://twitter.com/StatsOnTheT?ref_src=twsrc%5Etfw">@StatsOnTheT</a><br> presented such models at her <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> workshop <a href="https://t.co/YyQyUKFaxP">https://t.co/YyQyUKFaxP</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/975671735340158978?ref_src=twsrc%5Etfw">March 19, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

<blockquote class="twitter-tweet"><p lang="en" dir="ltr">New <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> post! &quot;Storrrify <a href="https://twitter.com/hashtag/satRdayCapeTown?src=hash&amp;ref_src=twsrc%5Etfw">#satRdayCapeTown</a> 2018&quot;<a href="https://t.co/1HimAko2yf">https://t.co/1HimAko2yf</a><br><br>Thanks again to the organizers, participants and sponsors of this great conference! <a href="https://t.co/HHJjhLUUR9">pic.twitter.com/HHJjhLUUR9</a></p>&mdash; Maëlle Salmon 🐟 (@ma_salmon) <a href="https://twitter.com/ma_salmon/status/977529134019641344?ref_src=twsrc%5Etfw">March 24, 2018</a></blockquote>
<script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>


