---
title: Is it wine o'clock?
date: '2017-08-12'
slug: wineoclock
comments: yes
---


Emojis were again quite _en vogue_ this week on Twitter with Romain François doing some [awesome stuff](https://twitter.com/romain_francois/status/896364762124234752) for the [`emo` package](https://github.com/hadley/emo), in particular this [teeny tiny animated clock](https://twitter.com/romain_francois/status/896030932356005888). It reminded me of my own emoji animated clock that I had done a while ago for representing time-use data. Time for me to present its genesis!

<!--more-->

I'm actually not a [Quantified Self](https://en.wikipedia.org/wiki/Quantified_Self) person, but at my work time-use data was collected [for an epidemiology project](http://www.sciencedirect.com/science/article/pii/S1438463917301876): information about people activities and locations throughout one day can help unraveling sources of exposure to air pollution. I've therefore spent some time thinking about how to represent such data. In particular, my colleague [Margaux](https://www.researchgate.net/profile/Margaux_Sanchez) directed a [fantastic video about our project](https://www.youtube.com/watch?v=LztOw_7MVPw). We introduced some real data from our project in it, including an animated clock that I made with emoji-coding of indoor-outdoor location. I'll present code and data for producing a similar clock with my own agenda on some Wednesday evenings of last year.

# Loading the time-use data

I logged data from my Wednesday in a rather ok format.


```r
library("magrittr")
date <- "2016-03-10"
activities <- readr::read_csv2("data/2017-08-12-wineoclock-timeuse.csv",
                               col_types = "ccc") %>%
  dplyr::mutate(start = lubridate::ymd_hms(paste0(date, start, ":00")),
         end = lubridate::ymd_hms(paste0(date, end, ":00")))
knitr::kable(activities)
```



|start               |end                 |activity   |
|:-------------------|:-------------------|:----------|
|2016-03-10 00:00:00 |2016-03-10 06:30:00 |sleeping   |
|2016-03-10 06:30:00 |2016-03-10 07:00:00 |coffee     |
|2016-03-10 07:00:00 |2016-03-10 08:00:00 |running    |
|2016-03-10 08:00:00 |2016-03-10 09:30:00 |train      |
|2016-03-10 09:30:00 |2016-03-10 17:00:00 |computer   |
|2016-03-10 17:00:00 |2016-03-10 18:00:00 |train      |
|2016-03-10 18:00:00 |2016-03-10 20:00:00 |school     |
|2016-03-10 20:00:00 |2016-03-10 20:30:00 |pizza      |
|2016-03-10 20:30:00 |2016-03-10 22:00:00 |dancer     |
|2016-03-10 22:00:00 |2016-03-10 22:30:00 |wine_glass |
|2016-03-10 22:30:00 |2016-03-10 23:59:00 |sleeping   |

When I prepared this visualization [`ggimage`](https://github.com/GuangchuangYu/ggimage) didn't exist so I knew I'd have to use emojis from [`emojifont`](https://github.com/GuangchuangYu/emojifont) to represent my activities, therefore I directly entered the activities as emojis. My Wednesdays at that time were quite varied, I started the day with breakfast (how original), then some time at the gym, followed by a rather short workday due to my taking a Catalan class at the end of the afternoon. The evening was spent enjoying a quick and cheap pizza dinner with my husband before our salsa class and sometimes treating ourselves to a wine glass at the small and rather shabby-looking bar at the end of our street, whose name really was Small bar.

## Making the animated clock

I didn't have to think about how to draw and animated a clock with `ggplot2` because somebody already had: I used code [from this gist](https://gist.github.com/drewconway/1142938) of [Drew Conway's](https://twitter.com/drewconway).


```r
# Generate digitial clock face
first_nine <- c('00', '01', '02', '03', '04', '05', '06', '07', '08', '09')
hours <- c(first_nine, as.character(seq(10,23,1)))
mins <- c(first_nine, as.character(seq(10,59,1)))
time_chars_l <- lapply(hours, function(h) paste(h, ':', mins, sep=''))
time_chars <- do.call(c, time_chars_l)

# Generate analog clock face
hour_pos <- seq(0, 12, 12/(12*60))[1:720]
min_pos <-seq(0,12, 12/60)[1:60]
hour_pos <- rep(hour_pos, 2)
all_times <- dplyr::tbl_df(cbind(hour_pos, min_pos)) %>%
  dplyr::mutate(index = time_chars) %>%
  dplyr::mutate(time = lubridate::ymd_hms(paste0(date, index, ":00"))) 
```

I then needed to join the `all_times` table, containing many snapshots of the time, with my time-use table made of intervals (start and end of each activity). Another package born in the meantime, [`fuzzyjoin`](https://github.com/dgrtwo/fuzzyjoin), would have allowed me to write more efficient code, and I was so sad when looking at the old code that I re-wrote it.


```r
all_times <- dplyr::mutate(all_times, start = time, end = time)
all_times  <- fuzzyjoin::interval_left_join(all_times, activities)
knitr::kable(all_times[1:10,])
```



|  hour_pos| min_pos|index |time                |start.x             |end.x               |start.y    |end.y               |activity |
|---------:|-------:|:-----|:-------------------|:-------------------|:-------------------|:----------|:-------------------|:--------|
| 0.0000000|     0.0|00:00 |2016-03-10 00:00:00 |2016-03-10 00:00:00 |2016-03-10 00:00:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.0166667|     0.2|00:01 |2016-03-10 00:01:00 |2016-03-10 00:01:00 |2016-03-10 00:01:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.0333333|     0.4|00:02 |2016-03-10 00:02:00 |2016-03-10 00:02:00 |2016-03-10 00:02:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.0500000|     0.6|00:03 |2016-03-10 00:03:00 |2016-03-10 00:03:00 |2016-03-10 00:03:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.0666667|     0.8|00:04 |2016-03-10 00:04:00 |2016-03-10 00:04:00 |2016-03-10 00:04:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.0833333|     1.0|00:05 |2016-03-10 00:05:00 |2016-03-10 00:05:00 |2016-03-10 00:05:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.1000000|     1.2|00:06 |2016-03-10 00:06:00 |2016-03-10 00:06:00 |2016-03-10 00:06:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.1166667|     1.4|00:07 |2016-03-10 00:07:00 |2016-03-10 00:07:00 |2016-03-10 00:07:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.1333333|     1.6|00:08 |2016-03-10 00:08:00 |2016-03-10 00:08:00 |2016-03-10 00:08:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |
| 0.1500000|     1.8|00:09 |2016-03-10 00:09:00 |2016-03-10 00:09:00 |2016-03-10 00:09:00 |2016-03-10 |2016-03-10 06:30:00 |sleeping |

Then I could continue using the code [from the gist](https://gist.github.com/drewconway/1142938), this step generating stuff for the two clock hands.


```r
all_times <- all_times %>% 
  dplyr::select(- start.x, - end.x, - start.y, - end.y) %>%
  tidyr::gather(name, time.info, 1:2) %>%
  dplyr::arrange(index) %>%
  dplyr::mutate(hands = ifelse(name == "hour_pos", 0.5, 1))
```

I decided to only plot a subset of my exciting day.


```r
all_times <- dplyr::filter(all_times, 
                           time > lubridate::ymd_hms("2016-03-10 15:00:00"),
                           time < lubridate::ymd_hms("2016-03-10 23:00:00") )
```

Last but not least, I plotted the clock. Nowadays I'd probably stick to [`magick`](https://github.com/ropensci/magick) for animating the plot but [`gganimate`](https://github.com/dgrtwo/gganimate) is awesome too anyway.


```r
library("ggplot2")
emojifont::load.emojifont('OpenSansEmoji.ttf')
clock <- ggplot(all_times, aes(xmin = time.info,
                               xmax = time.info + 0.03, 
                               ymin = 0,
                               ymax = hands,
                              frame = index))
clock <- clock + geom_rect()
clock <- clock + theme_bw()
clock <- clock + coord_polar()
clock <- clock + scale_x_continuous(limits = c(0,12), 
                                    breaks = 0:11,
                     labels = c(12, 1:11))
clock <- clock + scale_y_continuous(limits=c(0,1.1)) 
clock <- clock + coord_polar()
clock <- clock + geom_text(aes(x = 0,
                               y = 0,
                               label = emojifont::emoji(activity)),
                          family="OpenSansEmoji", 
                          size=20)
clock <- clock + theme(legend.position="none",
                      axis.text.y = element_blank(),
                      axis.ticks.y = element_blank(),
                      axis.title.y = element_blank(),
                      axis.title.x = element_blank(),
                      panel.background = element_blank(),
                      panel.border = element_blank(),
                      panel.grid.major = element_blank(),
                      panel.grid.minor = element_blank(),
                      plot.background = element_blank())

animation::ani.options(interval = 0.025)
gganimate::gganimate(clock, "clock.gif")
```

<img src="/figure/clock.gif" alt="clock" width="700">

I ended up with a very useful visualization as you can see... at least, I have no trouble finding out when wine o'clock is!
