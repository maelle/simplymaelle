---
title: A classical analysis (Radio Swiss classic program)
date: '2017-04-23'
slug: radioswissclassic
comments: yes
---


I am not a classical music expert at all, but I happen to have friends who are, and am even married to someone who plays the cello (and the ukulele!). I appreciate listening to such music from time to time, in particular Baroque music. A friend made me discover [Radio Swiss classic](http://www.radioswissclassic.ch/en), an online radio playing classical music all day and all night long, with a quite nice variety, and very little speaking between pieces, with no ads (thank you, funders of the radio!). Besides, the voices telling me which piece has just been played are really soothing, so Radio Swiss classic is a good one in my opinion. 

Today, instead of anxiously waiting for the results of the French presidential elections, I decided to download the program of the radio in the last years and have a quick look at it, since after all, the website says that the radio aims at relaxing people.

<!--more-->

# Scraping the program

My webscraping became a bit more elegant because I followed [the advice of EP alias expersso](https://twitter.com/expersso/status/839395958316232704), who by the way should really start blogging. I started downloading programs since September 2008 because that's when I met the friend who told me about Radio Swiss Classic.

```r
dates <- seq(from = lubridate::ymd("2008-09-01"),
             to = lubridate::ymd("2017-04-22"),
             by = "1 day")


base_url <- "http://www.radioswissclassic.ch/en/music-programme/search/"

get_one_day_program <- function(date, base_url){
  # in order to see progress
  message(date)
  
  # build URL
  date_as_string <- as.character(date)
  date_as_string <- stringr::str_replace_all(date_as_string, "-", "")
  url <- paste0(base_url, date_as_string)
  
  # read page
  page <- try(xml2::read_html(url),
              silent = TRUE)
  if(is(page, "try-error")){
    message("horribly wrong")

    closeAllConnections()
    return(NULL)
  }else{
    
    # find all times, artists and pieces
    times <- xml2::xml_text(xml2::xml_find_all(page, 
                                               xpath="//span[@class='time hidden-xs']//text()"))
    artists <- xml2::xml_text(xml2::xml_find_all(page, 
                                                 xpath="//span[@class='titletag']//text()"))
    pieces <- xml2::xml_text(xml2::xml_find_all(page, 
                                                xpath="//span[@class='artist']//text()"))
    # the last artist and piece are the current ones
    artists <- artists[1:(length(artists) - 1)]
    pieces <- pieces[1:(length(pieces) - 1)]
    
    # get a timedate from each time
    timedates <- paste(as.character(date), times)
    timedates <- lubridate::ymd_hm(timedates)
    timedates <- lubridate::force_tz(timedates, tz = "Europe/Zurich")
    
    # format the output
    program <- tibble::tibble(time = timedates,
                              artist = artists,
                              piece = pieces)
    
    return(program)
  }
  
}

programs <- purrr::map(dates, get_one_day_program, 
                       base_url = base_url)

programs <- dplyr::bind_rows(programs)

save(programs, file = "data/radioswissclassic_programs.RData")

```

There were some days without any program on the website, for which the website said something was horribly wrong with the server. 


```r
load("data/radioswissclassic_programs.RData")
wegot <- length(unique(lubridate::as_date(programs$time)))
wewanted <- length(seq(from = lubridate::ymd("2008-09-01"),
                       to = lubridate::ymd("2017-04-22"),
                       by = "1 day"))
```

However, I got a program for approximately 0.96 of the days.

# Who are the most popular composers?


```r
library("magrittr")
table(programs$artist) %>%
  broom::tidy() %>%
  dplyr::arrange(desc(Freq)) %>%
  head(n = 20) %>%
  knitr::kable()
```



|Var1                        |  Freq|
|:---------------------------|-----:|
|Wolfgang Amadeus Mozart     | 37823|
|Ludwig van Beethoven        | 20936|
|Joseph Haydn                | 18140|
|Franz Schubert              | 15596|
|Antonio Vivaldi             | 14947|
|Johann Sebastian Bach       | 12003|
|Felix Mendelssohn-Bartholdy | 11541|
|Antonin Dvorak              | 10265|
|Gioachino Rossini           |  9591|
|Frédéric Chopin             |  8470|
|Piotr Iljitsch Tchaikowsky  |  8092|
|Georg Friedrich Händel      |  7935|
|Tomaso Albinoni             |  6175|
|Gaetano Donizetti           |  5945|
|Giuseppe Verdi              |  5639|
|Johannes Brahms             |  5526|
|Johann Nepomuk Hummel       |  5439|
|Camille Saint-Saëns         |  5395|
|Luigi Boccherini            |  5130|
|Johann Christian Bach       |  4976|

I'll have to admit that I don't even know all the composers in this table but they're actually all famous according to my live-in classical music expert. Radio Swiss classic allows listeners to rate pieces, so the most popular ones are programmed more often, and well I guess the person making the programs also tend to program famous composers quite often.


```r
library("ggplot2")
library("hrbrthemes")
table(programs$artist) %>%
  broom::tidy() %>%
  ggplot() +
  geom_histogram(aes(Freq)) +
  scale_x_log10() +
  theme_ipsum(base_size = 14) 
```

![plot of chunk unnamed-chunk-3](/figure/source/2017-04-23-radioswissclassic/unnamed-chunk-3-1.png)

Interestingly, but not that surprisingly I guess given the popularity of, say, Mozart, the distribution of occurrences by composers seems to be log-normally distributed. 

# How long are pieces?

On the website of Radio Swiss classic it is stated that pieces are longer in the evening than during the day, which I wanted to try and see. Because the program of the radio was not corrected for time changes (i.e. on 25 hour-days there are only 24 hours of music according to the online program), I shall only look at pieces whose duration is smaller than 60 minutes, which solves the issue of missing days at the same time.


```r
programs <- dplyr::arrange(programs, time)
programs <- dplyr::mutate(programs,
                          duration = difftime(lead(time, 1),
                                       time,
                                       units = "min"))

programs <- dplyr::mutate(programs,
                          duration = ifelse(duration > 60,
                                            NA, duration))
programs <- dplyr::mutate(programs,
                          hour = as.factor(lubridate::hour(time)))

programs %>%
ggplot() +
  geom_boxplot(aes(hour, duration))+
  theme_ipsum(base_size = 14) 
```

![plot of chunk unnamed-chunk-4](/figure/source/2017-04-23-radioswissclassic/unnamed-chunk-4-1.png)

I don't find the difference between day and night that striking, maybe I could try to define day and night to have a prettier figure (but I won't do any test, I soon need to go watch TV).


```r
programs %>%
  dplyr::mutate(night = (lubridate::hour(time) <= 4 | lubridate::hour(time) >= 20)) %>%
ggplot() +
  geom_boxplot(aes(night, duration))+
  theme_ipsum(base_size = 14)
```

![plot of chunk unnamed-chunk-5](/figure/source/2017-04-23-radioswissclassic/unnamed-chunk-5-1.png)

# Conclusion

The website also states that the pieces are more lively in the morning, but I have no data to which to match the titles of the pieces to investigate that claim. Well I have not even looked for such data. Another extension that I would find interesting would be to match each composer's name to a style and then see how often each style is played. Now I'll stop relaxing and go stuff my face with food in front of the election results!
