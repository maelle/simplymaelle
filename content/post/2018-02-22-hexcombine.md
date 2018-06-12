---
title: Combine your hex stickers with magic(k)
date: '2018-02-22'
slug: 2018/02/22/hexcombine
comments: yes
tags: magick
---



Hex stickers remind me of [Pogs](https://en.wikipedia.org/wiki/Milk_caps_(game)), except they're cooler because you can combine them together! Some people do that very smartly.

<blockquote class="twitter-tweet" data-lang="ca"><p lang="en" dir="ltr">Now when I forget how to do an <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> analysis I can just check the back of my laptop <a href="https://twitter.com/hashtag/runconf16?src=hash&amp;ref_src=twsrc%5Etfw">#runconf16</a> <a href="https://t.co/DY7UgeBzYV">pic.twitter.com/DY7UgeBzYV</a></p>&mdash; David Robinson (@drob) <a href="https://twitter.com/drob/status/715694466707750913?ref_src=twsrc%5Etfw">1 d’abril de 2016</a></blockquote>


I've got a pretty random hex stickers combination on my laptop, but after all it could be worse...

<blockquote class="twitter-tweet" data-lang="ca"><p lang="en" dir="ltr">My boyfriend decided he is post-hex / post-<a href="https://twitter.com/hashtag/tidyverse?src=hash&amp;ref_src=twsrc%5Etfw">#tidyverse</a> <a href="https://t.co/ADtyORMxHL">pic.twitter.com/ADtyORMxHL</a></p>&mdash; Hilary Parker (@hspter) <a href="https://twitter.com/hspter/status/908102841323188225?ref_src=twsrc%5Etfw">13 de setembre de 2017</a></blockquote>

Now since I'm a `magick`/collage fan, you can bet I've wondered how to use R in order to combine stickers automatically! Say I have a bunch of sticker PNGs, how could I produce a map to design my laptop style? Read to find out more...

<!--more-->

# Getting some hex stickers to play with

I do agree that in real life you'll most often not have PNGs of the stickers but hey I can dream... especially since I'm sure that one will soon be able to identify stickers on a conference haul pic in the blink of an eye with the [`Rvision` package](https://github.com/swarm-lab/Rvision). I got stickers from [hex.bin](http://hexb.in/) whose existence I learnt from this tweet:

<blockquote class="twitter-tweet" data-lang="ca"><p lang="en" dir="ltr">A plea to my favorite <a href="https://twitter.com/hashtag/rstats?src=hash&amp;ref_src=twsrc%5Etfw">#rstats</a> 📦 developers: please submit your BIG hex sticker image files (with vector versions 🙏) to <a href="https://t.co/7KWg3d2fZA">https://t.co/7KWg3d2fZA</a>!  <a href="https://twitter.com/old_man_chester?ref_src=twsrc%5Etfw">@old_man_chester</a> <a href="https://twitter.com/nj_tierney?ref_src=twsrc%5Etfw">@nj_tierney</a> <a href="https://twitter.com/samfirke?ref_src=twsrc%5Etfw">@samfirke</a> they will be right at home 👇 <a href="https://t.co/AIqgnBIx1X">pic.twitter.com/AIqgnBIx1X</a></p>&mdash; Alison Hill (@apreshill) <a href="https://twitter.com/apreshill/status/966397975328038912?ref_src=twsrc%5Etfw">21 de febrer de 2018</a></blockquote>

And well if more R packages are added to it my `Rvision` dream could come true.

Note: When I say I got them from hex.bin, I mean I lazily downloaded the "hexagons/" folder of [this GitHub repository](https://github.com/maxogden/hexbin) by using [a link as explained in this SO answer](https://stackoverflow.com/a/38879691).

# Combining a few of them at random

I'll sample [42](https://en.wikipedia.org/wiki/Phrases_from_The_Hitchhiker%27s_Guide_to_the_Galaxy#Answer_to_the_Ultimate_Question_of_Life,_the_Universe,_and_Everything_(42)) stickers from my nice little internet haul.


```r
library("magrittr")
set.seed(42)
ultimate_sample <- fs::dir_ls("hexagons") %>%
  sample(size = 42)
```

After that I'll create rows which is pretty straightforward.


```r
no_row <- 6
no_col <- 7

row_paths <- split(ultimate_sample, rep(1:no_col, each = no_row))

# let's be crazy

read_append <- . %>%
  magick::image_read() %>%
  magick::image_append()

rows <- purrr::map(row_paths, read_append)
```

Now rows have to be combined together, not just stacked, with a small shift. This will depend on the size of pics. They seem to be the same size which is great. Note that the thing that gets displayed below is what `magick::image_info` gives you about any `magick` object, so I'll use that.


```r
magick::image_read(ultimate_sample[1:10])
info <- magick::image_read(ultimate_sample[1]) %>%
  magick::image_info()
height <- info$height
width <- info$width
```

The `col` parameter below is my laptop background _hex code_. I needed it before pasting the _hex stickers_.


```r
my_laptop <- magick::image_blank(width = width * no_col,
                    height = height * no_row * 0.9,
                    col = "#FF6987")

for(i in 1:no_row){
  if(i/2 == floor(i/2)){
    offset1 <- 0
  }else{
    offset1 <- (width/2) 
  }
  
  offset2 <- (i-1)*(height*0.75)
  
  my_laptop <- magick::image_composite(my_laptop, rows[[i]],
                                       offset = paste0("+", offset1,
                                                       "+", offset2))
}

magick::image_write(my_laptop, "data/my_laptop.png")
```
<img src="/figure/my_laptop.png" alt="my laptop" width="700">

This looks fine, the only reason why it's not perfect is probably the stickers not having exactly the same dimensions. Moreover, one of them doesn't have transparent borders! That's a mean design. [Jeroen Ooms](https://github.com/jeroen) told me I could correct it by smartly using `image_fill` with color = “transparent” and some fuzz and `image_trim` before that to remove margins but I was lazy.

Now, what could one do? One could order hex stickers by color using the code in my [blog post about rainbowing a set of pictures](http://www.masalmon.eu/2018/01/07/rainbowing/). Or one could just combine all hex stickers together, then make them gray and use the resulting image as a place to collect stickers. #goals


```r
magick::image_convert(my_laptop, colorspace = "Gray") %>%
  magick::image_write("data/my_laptop_goals.png")
```

<img src="/figure/my_laptop_goals.png" alt="laptop goals" width="700">

