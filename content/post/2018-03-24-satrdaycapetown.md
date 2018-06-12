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

{% include satrdaycapetown.html %}
