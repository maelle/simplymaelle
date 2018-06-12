---
title: Rainbowing a set of pictures
slug: content/post/2018-01-07-rainbowing
comments: yes
---


I've now done a few collages from R using `magick`: [the faces of #rstats Twitter](http://www.masalmon.eu/2017/03/19/facesofr/), [We R-Ladies](http://livefreeordichotomize.com/2017/07/18/the-making-of-we-r-ladies/) with Lucy D'Agostino McGowan, and [a holiday card for R-Ladies](https://github.com/rladies/rladies_holidays). The faces of #rstats Twitter and holiday card collages were arranged at random, while the We R-Ladies one was a mosaic forming the R-Ladies logo. I got the idea to up my collage skills by trying to learn how to arrange pics by their main colour, like a rainbow. The verb rainbow doesn't exist, and ["rainbowing"](https://en.wikipedia.org/wiki/Rainbowing) doesn't mean ordering by colour, but I didn't let this stop me.

It was the occasion to grab some useful knowledge about colours, not useless for someone who did not even know about [Pantone's Colors of the Year](https://en.wikipedia.org/wiki/Pantone#Color_of_the_Year) a few weeks ago...

_This post has nothing to do with Kesha's new album. However, you can listen to it while reading since it's so good, but maybe switch to something older from her when I use "$"._

<!--more-->

# Getting some pics to play with

The first pictures I tried to arrange were all the pictures ever posted by R-Ladies local chapters on their Twitter account. While it was fun to grab them all, it was not very interesting to play with them as so many of them were pictures of screens. I therefore grabbed "nature" pictures from [Pexels](https://www.pexels.com/) using the same method [as when creating the Bubblegum Puppies] following [this Stack Overflow thread](https://stackoverflow.com/questions/29861117/r-rvest-scraping-a-dynamic-ecommerce-page). I chose "nature" as a keyword because 1) it lead to many hits 2) it offered a good variety of colours.

```r
library("rvest")
library("RSelenium")
library("magrittr")

rD <- rsDriver()
remDr <- rD[["client"]]


# open the webpage
remDr$navigate("https://www.pexels.com/search/nature/")

# scroll down
for(i in 1:130){      
  remDr$executeScript(paste("scroll(0,",i*10000,");"),
                      args = list("dummy"))
  # be nice and wait
  Sys.sleep(1)    
}

# https://www.pexels.com/faq/

page_content <- remDr$getPageSource() 
remDr$close()

get_link_from_src <- function(node){
  xml2::xml_attrs(node)["src"] %>%
    as.character() %>%
    stringr::str_replace("\\?h.*", "")
  
}

xtract_pic_links <- function(source) {
  css <- '.photo-item__img'
  read_html(source[[1]]) %>%
    html_nodes(css) %>%
    purrr::map_chr(get_link_from_src)    
}

links <- xtract_pic_links(page_content)
links <- links[1:1400]

# save
dir.create("nature")
save_pic <- function(url){
  Sys.sleep(1)
  name <- stringr::str_replace(url, ".*\\/", "")
  
  try(magick::image_read(url) %>%
        magick::image_write(paste0("nature/", name)),
      silent = TRUE)
}

purrr::walk(links, save_pic)

```


# Extracting the main colour and making pics size-compatible

In the following code, I extracted the main colour from each pic using [Russel Dinnage's method](http://www.mepheoscience.com/colourful-ecology-part-1-extracting-colours-from-an-image-and-selecting-them-using-community-phylogenetics-theory/) as presented [in this blog post from David Smith](http://blog.revolutionanalytics.com/2015/03/color-extraction-with-r.html). Before that I had to install two packages from Github, [`rblocks`](https://github.com/ramnathv/rblocks) and [`rPlotter`](https://github.com/woobe/rPlotter). 

This code also serves another role: since I wanted to paste pics together at some point, I decided to make them all of the same dimensions by adding a border with `magick`. I had learnt how to do that when preparing [R-Ladies Global holiday card](https://github.com/rladies/rladies_holidays), but this time instead of using the same colour every time (R-Ladies' official purple), I used the main colour I'd just extracted. The most important points to make a picture a square are to know `magick::image_info` gives you the height and width of an image... and to somehow understand geometry which was embarrassingly a hurdle when I did that.

The code to extract colours didn't work in a few cases which I did not investigate a lot: I had downloaded more pics than what I needed because I had experienced the issue when working with R-Ladies meetups pics, and had seen it was because of seemingly bicolor pics.

```r
dir.create("formatted_pics")

format_image <- function(path){
  image <- magick::image_read(path)
  info <- magick::image_info(image)
  
  # find in which direction I need to add pixels
  # to make this a square
  direction <- ifelse(info$height > info$width,
                      "height", "width")
  scale_number <- as.numeric(info[direction]/500)
  image <- magick::image_scale(image, paste0(info["width"]/scale_number,
                                             "x", 
                                             info["height"]/scale_number))
  newinfo <- magick::image_info(image)
  
  # colours
  colours <- try(rPlotter::extract_colours(path, num_col = 1), silent = TRUE)
  
  # one pic at least was problematic 
  if(!is(colours, "try-error")){
    colour <- colours[1]
    
    image <- magick::image_border(image, colour, paste0((500-newinfo$width)/2, "x",
                                                        (500-newinfo$height)/2))
    info <- magick::image_info(image)
    # odd numbers out!
    if(info$height/2 != floor(info$height/2)){
      image <- magick::image_crop(image, "0x500+0")
    }
    if(info$width/2 != floor(info$width/2)){
      image <- magick::image_crop(image, "500x0+0")
    }
    magick::image_write(image,
                        stringr::str_replace(path, "nature", "formatted_pics"))
    tibble::tibble(path = path,
                   colour = colour)
  }else{
    NULL
  }
  
  
  
}

pics_main_colours <- purrr::map_df(dir("nature", full.names = TRUE), format_image)
readr::write_csv(pics_main_colours, path = "pics_main_colours.csv")
```
And because I'm apparently a bad planner, I had to reduce pictures afterwards.

```r
# we need smaller images
reduce_image <- function(path){
  magick::image_read(path) %>%
    magick::image_scale("50x50!") %>%
    magick::image_write(path)
}

purrr::walk(dir("formatted_pics", full.names = TRUE),
            reduce_image)
```

# Preparing a function to order and paste pictures

This function has a collage part which you might recognize from my [the faces of #rstats Twitter](http://www.masalmon.eu/2017/03/19/facesofr/) blog post, and a ordering pictures according to a variable part that's new and uses a bit of tidy eval...  Maybe I'll really learn tidy eval this year! `pics_info` needs to be a data.frame with the path to pictures and well the variable one wants to use to order them.

```r

library("rlang")

make_column <- function(i, files, no_rows){
  magick::image_read(files[(i*no_rows+1):((i+1)*no_rows)]) %>%
    magick::image_append(stack = TRUE) %>%
    magick::image_write(paste0("cols/", i, ".jpg"))
}


make_collage <- function(pics_info, no_rows, no_cols, ordering_col){
  pics_info <- dplyr::arrange(pics_info, !!!syms(ordering_col))
  pics_info <- pics_info[1:(no_rows*no_cols),]
  pics_info <- dplyr::mutate(pics_info, column = rep(1:no_cols, each = no_rows))
  pics_info <- dplyr::group_by(pics_info, column) %>%
    dplyr::arrange(!!!syms(ordering_col)) %>%
    dplyr::mutate(row = 1:no_rows) %>%
    dplyr::ungroup()
  
  pics_info <- dplyr::arrange(pics_info, column, row)
  
  dir.create("cols")
  purrr::walk(0:(no_cols-1), make_column, files = pics_info$path,
              no_rows = no_rows)
  
  banner <- magick::image_read(dir("cols/", full.names = TRUE)) %>%
    magick::image_append(stack = FALSE) 
  
  unlink("cols", recursive = TRUE)
  
  return(banner)
  
}

```

The function returns a `magick` object that one can then write to disk as a PNG for instance.

I first tested it using a random approach added to the data.frame created in the next section, and show the result here to give an idea of the variety of pictures. For many of them, however, the main colour that you can see in their border is greyish.


```r
set.seed(42)
pics_info <- dplyr::mutate(pics_info, random = sample(1:nrow(pics_info), nrow(pics_info)))
make_collage(pics_info, 19, 59, "random") %>% 
  magick::image_write("data/2018-01-07-rainbowing-banner_random.png")
```` 
![](https://raw.githubusercontent.com/maelle/maelle.github.io/master/_source/data/2018-01-07-rainbowing-banner_random.png)


# Testing a first (bad) approach: using hue

Once I had the main colour as an hex code, I had no idea how to order the colours and thought a good idea would be to use hue, which is the main wave length in a colour. Most observed colours are a mix of wave lengths unless you're using a laser for instance. To get hue from a colour identified by its hex code, one needs two functions: `colorspace::hex2rgb` and `grDevices::rgb2hsv`. The latter one outputs hue, saturation and value. Hue is the main wavelength, saturation the amount of that wavelength in the colour and value the amount of light in the colour. The smaller the saturation, the less representative the hue is of the main colour. Add to that the fact that the main colour can also be only a little representative of your original picture... Ordering by hue isn't too perfect, but I tried that anyway. 

```r
# now work on getting the hue and value for all pics
# create a data.frame with path, hue, value 
get_values <- function(path, pics_main_colours){
  print(path)
  # get main color
  colour <- pics_main_colours$colour[pics_main_colours$path == stringr::str_replace(path,
                                                                      "formatted_pics",
                                                                      "nature")]
  
  # translate it
  rgb <- colorspace::hex2RGB(colour)
  values <- grDevices::rgb2hsv(t(rgb@coords))
  
  tibble::tibble(path = path,
                 hue = values[1,1],
                 saturation = values [2,1],
                 value = values[3,1])
}

# all values
pics_col <- purrr::map_df(dir("formatted_pics", full.names = TRUE),
                          get_values, pics_main_colours)
                          



make_collage(pics_info, 19, 59, "hue") %>% 
  magick::image_write("banner_hue.png")

```
![](https://raw.githubusercontent.com/maelle/maelle.github.io/master/_source/data/2018-01-07-rainbowing-banner_hue.png)

So this is not too pretty. Blue and green pictures seem to cluster together but there are very dark pictures which we'd intuitively put aside.

So I thought a bit more and decided to first assign main colours to a reference colour and then order pictures based on this...

# Choosing a better approach: RGB and distances

The first challenge was to choose reference colours which'd be a rainbow slices. I could have looked up RGB codes corresponding to [ROYGBIV (red, orange, yellow, green, blue, indigo and violet.)](https://en.wikipedia.org/wiki/ROYGBIV) but I had read about [xkcd colors survey](https://blog.xkcd.com/2010/05/03/color-survey-results/) in [this interesting post](https://blog.algolia.com/how-we-handled-color-identification/) and therefore decided to use XKCD colors, available in R via the `xkcdcolors` package. I chose to use the 18 most common ones, and add black to that lot. It was no longer really a rainbow, I agree. The colors present in the pictures were ordered by hand by my husband, and I like his choices.

Then to assign each pic to a reference colour via its main colour, I calculated the Euclidian distance between that colour and all reference colours to find the closes reference colours, using the RGB values.

```r
library("xkcdcolors")
library("magrittr")
# version of colorspace::hex2RGB returning a df
hex2rgb <- function(hex){
  result <- colorspace::hex2RGB(hex)@coords
}

# https://stackoverflow.com/questions/45328221/unnest-one-column-list-to-many-columns-in-tidyr
colors <- tibble::tibble(name = c(xcolors()[1:18], "black"),
                         hex = name2color(name),
                         rgb = purrr::map(hex, hex2rgb)) %>% 
  dplyr::mutate(rgb = purrr::map(rgb, tibble::as_tibble)) %>%
  tidyr::unnest()

# for each colour I want the closest one.
find_closest_colour <- function(hex, colors){
  test <- tibble::tibble(hex = hex) %>% 
    dplyr::mutate(rgb = purrr::map(hex, hex2rgb),
                  rgb = purrr::map(rgb, tibble::as_tibble)) %>%
    tidyr::unnest() 
  
  distance <- stats::dist(rbind(test[, c("R", "G", "B")],
                                colors[, c("R", "G", "B")]))
  
  colors$name[which.min(as.matrix(distance)[,1][2:(nrow(colors) + 1)])]
}

imgs_col <- dplyr::mutate(pics_main_colours,
                          xkcd_col = purrr::map_chr(colour, find_closest_colour,
                                                    colors = colors))


readr::write_csv(imgs_col, path = "imgs_xkcd_col.csv")
```

Once I had this information about each pic, I could order the pictures, after having defined the order of the reference colours. 


```r
pics_info <- readr::read_csv("imgs_xkcd_col.csv")
pics_info <- dplyr::mutate(pics_info, 
                           path = stringr::str_replace(path, "nature", "formatted_pics"))

pics_info <- dplyr::mutate(pics_info,
                           xkcd_col = factor(xkcd_col, ordered = TRUE,
                                             levels = c("black","brown","red","magenta","pink",
                                                        "lime green","green","dark green","teal",
                                                        "light blue","sky blue","blue","purple","grey")))

```

![](https://raw.githubusercontent.com/maelle/maelle.github.io/master/_source/data/2018-01-07-rainbowing-banner_xkcd.png)

This looks much better, but of course the initial set (and maybe the used extraction method as well) don't provide for enough colours to make this extremely pretty. I'm not sure how useful this end product is, but hey I got to look at pretty landscapes full of colours from my grey rainy city, and learnt a lot along the way... Besides, maybe _you_ will find a cool use case of some of the colour methods featured here and will tell me about it in the comments?
