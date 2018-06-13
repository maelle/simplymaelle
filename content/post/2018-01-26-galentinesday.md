---
title: Galentine's day cards
date: '2018-01-26'
tags:
  - magick
  - praise
  - rcorpora
slug: galentinesday
comments: yes
---


Remember the [nascent series of blog posts about Parks and recreation](/2017/11/05/newborn-serie/)? Well, we're still at one post, but don't worry, here is a new one, and I'm sure the series will eventually be a real one. I'm looking at you, my R-Ladies friends. That said, today is not a day for passive agressive hints, because I've decided it's [Galentine's day](https://en.wikipedia.org/wiki/Galentine%27s_Day) and I'll show you how to craft cards for your R-Ladies friends from your R prompt!


<!--more-->

Galentine's day is a celebration Leslie Knope throws every year for her female friends, showering them with love and gifts. Since she's very talented, she can even write them poems, but in the R version of Galentine's day, we'll make do with simple cards featuring Lesliesque compliments. Indeed, Leslie Knope is also known for her [creative complimenting](https://www.theodysseyonline.com/14-leslie-knope-compliments-ann-perkins-life) of her best friend Ann. I've decided to adopt her tone by using three praising adjectives followed by an animal name, and to dedicate them to imaginary friends called Ann, Donna, April and Shauna. Had I not decided to steal names from the TV show, I could have used the `charlatan::ch_name()` from the [rOpenSci `charlatan` package](https://github.com/ropensci/charlatan), possibly extracting first names thanks to the [`humaniformat` package](https://cran.r-project.org/web/packages/humaniformat/index.html).

# Create the compliments

Here I need animal names and praising adjectives for a pretty frivolous use case. Now sometimes you could want to randomly draw words to make identifiers for, say, participants in your epidemiological study because that'd be easier to deal with than numbers. The [`ids` package](https://github.com/richfitz/ids) actually provides human readable identifiers in the style `<adjective>_<animal>` (following [gfycat.com](https://gfycat.com/)) but sadly that adjective wouldn't necessary be positive, so I'll use the [`praise` package](https://github.com/rladies/praise) to get adjectives and the [`rcorpora` package](https://github.com/gaborcsardi/rcorpora) to get animal names. I'll set the [random seed](/2017/04/12/seeds/) to ensure reproducibility, not forgetting to be careful about [where to generate it](http://livefreeordichotomize.com/2018/01/22/a-set-seed-ggplot2-adventure/).


```r
set.seed(123)

compliment <- function(name, animal){
  praise::praise(paste0(name, ", you ${adjective}, ${adjective}, ${adjective} ", animal))
}

compliments <- tibble::tibble(name = c("Ann", "April", "Donna", "Shauna"),
                              animal = sample(rcorpora::corpora(which = "animals/common")$animals, size = 4),
                              compliment = purrr::map2_chr(name, animal, compliment))
```


# Create the cards

I first thought I could use cute animal pics for the cards, my husband even suggested I use the animal from the compliment which I could have done using Pexels as in my [bubblegum puppies post](/2018/01/04/bubblegumpuppies/) or my [rainbowing post](/2018/01/07/rainbowing/), using the [gist posted by Bob Rudis to avoid using `RSelenium`](https://gist.github.com/hrbrmstr/4cabe4af87bd2c5fe664b0b44a574366), but I decided to go a simpler route (which I'll call [using Kasia's strategy to get stuff done](https://kkulma.github.io/2017-12-29-end-of-year-thoughts/)), generating random colours, and putting text on a single-colour background. Let's assume Leslie Knope would have written a poem on the other side, which you could do using [Katie Jolly's approach](http://katiejolly.io/blog/2018-01-05/random-rupi-markov-chain-poems). Anyway, how did I generate random colours? Well, using `charlatan`! Really, a quite useful package by Scott Chamberlain, peer-reviewed by Brooke Anderson and TJ Mahr at [rOpenSci onboarding repo](https://github.com/ropensci/onboarding/issues/94).


```r
set.seed(123)
compliments <- dplyr::mutate(compliments, 
                             background = charlatan::ch_hex_color(n = 4), 
                             text_colour = charlatan::ch_hex_color(n = 4))
```

And then I could use [`magick`](https://github.com/ropensci/magick)! I've convinced myself that although I use `magick` for non serious stuff, I could use these skills on scientific image data one day. Note that I could have packed the whole code of the post in a single function, but I wanted to progressively build the card (compliments, then colours, then the image).


```r
library("magrittr")

create_card <- function(who, compliments){
  compliments <- dplyr::filter(compliments, name == who)
  magick::image_blank(600, 400,
                      color = compliments$background) %>%
    magick::image_annotate(text = compliments$compliment,
                           color = compliments$text_colour,
                           location = "+50+100",
                           boxcolor = "white",
                           size = 20) %>%
    magick::image_annotate(text = "Love, Leslie",
                           color = compliments$text_colour,
                           location = "+200+300",
                           boxcolor = "white",
                           size = 20)%>%
    magick::image_annotate(text = "HAPPY GALENTINE'S DAY!",
                           boxcolor = compliments$text_colour,
                           location = "+100+200",
                           color = "white",
                           size = 20) %>%
    magick::image_write(paste0("data/galentines", who, ".png"))
}

purrr::walk(compliments$name, create_card, compliments)
```

Yes, I signed the cards "Leslie". I know the text could be centered more but I don't really care.

# Display the cards

In real life, you'd print and send these cards to your friends, but since my imaginary friends Ann, April, Donna and Shauna probably read my blog, I'll let the cards here for them to see!

<img src="/figure/galentinesAnn.png" alt="galentines Ann" width="700">

<img src="/figure/galentinesApril.png" alt="galentines April" width="700">

<img src="/figure/galentinesDonna.png" alt="galentines Donna" width="700">

<img src="/figure/galentinesShauna.png" alt="galentines Shauna" width="700">


Not too shabby for random text and colours!
