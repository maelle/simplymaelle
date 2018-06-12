---
layout: post
title: "Where to live in the US"
comments: true
---


I was fascinated by this [xkcd comic](https://xkcd.com/1916/) about where to live based on your temperature preferences. I also thought it'd be fun to try to make a similar one from my R session! Since I'm no meteorologist and was a bit unsure of how to define winter and summer, and of their relevance in countries like, say, India which has monsoon, I decided to focus on a single country located in one hemisphere only and big enough to offer some variety... the USA! So, dear American readers, where should you live based on your temperature preferences?

<!--more-->

# Defining data sources

## Weather data

The data for the original xkcd graph comes from [weatherbase](http://www.weatherbase.com/). I changed sources because 1) I was not patient enough to wait for weatherbase to send me a custom dataset which I imagine is what xkcd author did and 2) I'm the creator of [a cool package accessing airport weather data](http://ropensci.github.io/riem/) for the whole word including the US! My package is called "riem" like "R Iowa Environmental Mesonet" (the source of the data, a [fantastic website](https://mesonet.agron.iastate.edu/request/download.phtml?network=IN__ASOS)) and "we laugh" in Catalan (at the time I wrote the package I was living in Barcelona and taking 4 hours of Catalan classes a week!). It's a simple but good package which underwent [peer-review](https://github.com/ropensci/onboarding/issues/39) at rOpenSci onboarding, thank you [Brooke](https://twitter.com/gbwanderson)!

I based my graph on data from the last winter and the last summer. I reckon that one should average over more years, but nothing important is at stake here, right? 

## Cities sample

My package has a function for downloading weather data for a given airport based on its ID. For instance Los Angeles airport is LAX. At that point I just needed a list of cities in the US with their airport code. Indeed with my package you can get all airport weather networks, one per US state, and all the stations within that network... this is a lot of airports with no way to automatically determine how big they are! And to get the city name since it'd be so hard to parse the airport name I'd have resorted to geocoding with [e.g. this package](https://github.com/ropensci/opencage). A bit complicated for a simple fun graph!

So I went to the [Wikipedia page of the busiest airports in the US](https://en.wikipedia.org/wiki/List_of_the_busiest_airports_in_the_United_States) and ended up getting [this dataset](https://www.bts.gov/content/passengers-boarded-top-50-us-airports) from the US Department of Transportation (such a weird open data format by the way... I basically copy-pasted the first two columns in a spreadsheet!). This is not perfect, getting a list of major cities in every state would be more fair but hey reader I want you to live in a really big city so that I might know where it is.

## Ok so let's get the data


```r
us_airports <- readr::read_csv("data/2017-11-16_wheretoliveus_airports.csv")
knitr::kable(us_airports[1:10,])
```



|Name                                                    |Code |
|:-------------------------------------------------------|:----|
|Atlanta, GA (Hartsfield-Jackson Atlanta International)  |ATL  |
|Los Angeles, CA (Los Angeles International)             |LAX  |
|Chicago, IL (Chicago O'Hare International)              |ORD  |
|Dallas/Fort Worth, TX (Dallas/Fort Worth International) |DFW  |
|New York, NY (John F. Kennedy International)            |JFK  |
|Denver, CO (Denver International)                       |DEN  |
|San Francisco, CA (San Francisco International)         |SFO  |
|Charlotte, NC (Charlotte Douglas International)         |CLT  |
|Las Vegas, NV (McCarran International)                  |LAS  |
|Phoenix, AZ (Phoenix Sky Harbor International)          |PHX  |

I first opened my table of 50 airports. Then, I made the call to the Iowa Environment Mesonet.

```r
summer_weather <- purrr::map_df(us_airports$Code, riem::riem_measures,
                                date_start = "2017-06-01",
                                date_end = "2017-08-31")
winter_weather <- purrr::map_df(us_airports$Code, riem::riem_measures,
                                date_start = "2016-12-01",
                                date_end = "2017-02-28")
```

We then remove the lines that will be useless for further computations (The xkcd graph uses average temperature in the winter and average Humidex in the summer which uses both temperature and [dew point](https://en.wikipedia.org/wiki/Dew_point)).

```r

summer_weather <- dplyr::filter(summer_weather,
                                !is.na(tmpf), !is.na(dwpf))

winter_weather <- dplyr::filter(winter_weather,
                                !is.na(tmpf))
```



I quickly checked that there was "nearly no missing data" so I didn't remove any station nor day but if I were doing this analysis for something serious I'd do more checks including the time difference between measures for instance. Note that I end up with 48 airports only, there was no measure for Honolulu, HI (Honolulu International), San Juan, PR (Luis Munoz Marin International). Too bad!

# Calculating the two weather values

I started by converting all temperatures to Celsius degrees because although I like you, American readers, Fahrenheit degrees do not mean anything to me which is problematic for checking results plausibility for instance. I was lazy and just used Brooke Anderson's (yep the same Brooke who reviewed `riem` for rOpenSci) [`weathermetrics` package](https://github.com/geanders/weathermetrics/).


```r
summer_weather <- dplyr::mutate(summer_weather,
                                tmpc = weathermetrics::convert_temperature(tmpf, 
                                                                           old_metric = "f", new_metric = "c"),
                                dwpc = weathermetrics::convert_temperature(dwpf, 
                                                                           old_metric = "f", new_metric = "c")) 

winter_weather <- dplyr::mutate(winter_weather,
                                tmpc = weathermetrics::convert_temperature(tmpf, 
                                                                           old_metric = "f", new_metric = "c"))
```

## Summer values

Summer values are Humidex values. The author explained Humidex "combines heat and [dew point](https://en.wikipedia.org/wiki/Dew_point)". Once again I went to [Wikipedia](https://en.wikipedia.org/wiki/Humidex) and found the formula. I'm not in a mood to fiddle with formula writing on the blog so please go there if you want to see it. There's also a package with a [function returning the Humidex](https://www.rdocumentation.org/packages/comf/versions/0.1.7/topics/calcHumidity). I was feeling adventurous and therefore wrote my own function and checked the results with the numbers from Wikipedia. It was first wrong because I wrote a "+" instead of a "-"... checking one's code is crucial.


```r
calculate_humidex <- function(temp, dewpoint){
  temp + 0.5555*(6.11 *exp(5417.7530*(1/273.16 - 1/(273.15 + dewpoint))) - 10)
}

calculate_humidex(30, 15)
```

```
## [1] 33.969
```

```r
calculate_humidex(30, 25)
```

```
## [1] 42.33841
```

And then calculating the summer Humidex values by station was quite straightforward...


```r
library("magrittr")
summer_weather <- dplyr::mutate(summer_weather,
                                humidex = calculate_humidex(tmpc, dwpc))
summer_values <- summer_weather %>%
  dplyr::group_by(station) %>%
  dplyr::summarise(summer_humidex = mean(humidex, na.rm = TRUE))
```

... as it was for winter temperatures.


```r
winter_values <- winter_weather %>%
  dplyr::group_by(station) %>%
  dplyr::summarise(winter_tmpc = mean(tmpc, na.rm = TRUE))
```

# Prepare data for plotting

I first joined winter and summer values


```r
climates <- dplyr::left_join(winter_values, summer_values,
                             by = "station")
```

Then I re-added city airport names.


```r
climates <- dplyr::left_join(climates, us_airports,
                             by = c("station" = "Code"))
```

I only kept the city name.


```r
head(climates$Name)
```

```
## [1] "Atlanta, GA (Hartsfield-Jackson Atlanta International)"              
## [2] "Austin, TX (Austin - Bergstrom International)"                       
## [3] "Nashville, TN (Nashville International)"                             
## [4] "Boston, MA (Logan International)"                                    
## [5] "Baltimore, MD (Baltimore/Washington International Thurgood Marshall)"
## [6] "Cleveland, OH (Cleveland-Hopkins International)"
```

```r
climates <- dplyr::mutate(climates, 
                          city = stringr::str_replace(Name, " \\(.*", ""))
head(climates$city)
```

```
## [1] "Atlanta, GA"   "Austin, TX"    "Nashville, TN" "Boston, MA"   
## [5] "Baltimore, MD" "Cleveland, OH"
```

# Plotting!

When imitating an xkcd graph, one should use the `xkcd` package! I had already done that [in this post](http://www.masalmon.eu/2017/05/14/evergreenreviewgraph/). 


```r
library("xkcd")
library("ggplot2")
library("extrafont")
library("ggrepel")

xrange <- range(climates$summer_humidex)
yrange <- range(climates$winter_tmpc)

set.seed(42)

ggplot(climates,
       aes(summer_humidex, winter_tmpc)) +
  geom_point() +
  geom_text_repel(aes(label = city),
                   family = "xkcd", 
                   max.iter = 50000)+
  ggtitle("Where to live based on your temperature preferences",
          subtitle = "Data from airports weather stations, 2016-2017") +
  xlab("Summer heat and humidity via Humidex")+
  ylab("Winter temperature in Celsius degrees") +
  xkcdaxis(xrange = xrange,
           yrange = yrange)+
  theme_xkcd() +
  theme(text = element_text(size = 16, family = "xkcd"))
```

![plot of chunk unnamed-chunk-10](/figure/source/2017-11-16-wheretoliveus/unnamed-chunk-10-1.png)

So, which city seems attractive to you based on this plot? Or would you use different weather measures, e.g. do you care about rain?
