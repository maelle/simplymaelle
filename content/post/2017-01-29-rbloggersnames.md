---
title: The Rt of naming your blog
date: '2017-01-29'
tags:
  - rvest
  - webscraping
slug: rbloggersnames
comments: yes
---

In this post, I'm sharing a brand-new analysis! The reason for this is my blog being added to [R-bloggers](https://www.r-bloggers.com/) by Tal Galili after I filled [this form](https://www.r-bloggers.com/add-your-blog/). R-bloggers is a collection of blogs about R, whose new posts get added to the website via the magic of RSS feeds. R-bloggers even has a [Twitter account](https://twitter.com/Rbloggers). As a reader of R-bloggers you get exposed to many different analyses and ideas, as a R-blogger you reach a wider audience, so really it's an useful website. Tal does a great job maintaining R-bloggers and understandably likes seeing R-bloggers mentioning the website on their blog, which I already do in the [About section](/about/), and in one article, which I've consistently failed to do in the last two posts because I got too caught up about the article at hand to think about anything else. So I've figured out the best way not to forget to thank Tal for his work was to do an analysis _about_ R-bloggers! Genius, I know. I've scraped the [full list of contributing blogs](https://www.r-bloggers.com/blogs-list/) and had a look at their names and addresses. 

<!--more-->

# Scraping and transforming the list of blogs

The list of contributing blogs is not an html table which makes me things more interesting. I had to look a bit at the source of the html page to come up with a solution to transform it. I wasn't too slow because I've done something similar with Wikipedia data in the past, something that's still an unfinished building, of course, but whose code I could re-use. 

I used [`rvest`](https://github.com/hadley/rvest) for first scraping the data but also [`stringr`](https://github.com/tidyverse/stringr) for manipulating strings. What I like the most about `stringr` compared to manipulating strings with base R are the names of functions, that reflect what they do so well, and the order of arguments, with the `character` vector first. [Nick](https://twitter.com/nj_tierney), who has [a great R blog](http://www.njtierney.com/) by the way, told me in the comments [of one post](/2017/01/26/morewater/) I had "some pretty ninja grep skills". Although I think he exaggerates a little bit, I'll try not to disappoint him!

First I wanted to get a `character` vector for each blog.


```r
library("rvest")
library("purrr")
library("tibble")
library("stringr")
blogs_list <- read_html("https://www.r-bloggers.com/blogs-list/")
blogs_list
```

```
## {xml_document}
## <html xmlns="http://www.w3.org/1999/xhtml" lang="en-US" prefix="og: http://ogp.me/ns#" itemscope="" itemtype="http://schema.org/WebSite">
## [1] <head profile="http://gmpg.org/xfn/11">\n  <meta http-equiv="Content ...
## [2] <body class="page page-id-16581 page-template page-template-page-lin ...
```

```r
blogs_list <- html_nodes(blogs_list, "ul") 
blogs_list
```

```
## {xml_nodeset (10)}
##  [1] <ul id="menu-top-nav" class="sf-menu"><li id="menu-item-48314" clas ...
##  [2] <ul class="sub-menu"><li id="menu-item-76945" class="menu-item menu ...
##  [3] <ul>\n  <li>\n    <a class="rsswidget" href="http://feedproxy.googl ...
##  [4] <ul><li><a href="https://www.r-bloggers.com/search/git">git</a></li ...
##  [5] <ul><li>\n\t\t\t\t<a href="https://www.r-bloggers.com/trumpworld-an ...
##  [6] <ul class="xoxo blogroll"><li><a href="http://www.proc-x.com/" oncl ...
##  [7] <ul class="xoxo blogroll"><li><a href="#"></a></li>\n<li><a href="# ...
##  [8] <ul class="xoxo blogroll"><li><a href="https://www.r-users.com/" on ...
##  [9] <ul><li>\n\t\t\t\t\t\t\t\t\t\t<a href="https://www.r-bloggers.com/v ...
## [10] <ul>\n  <li>\n    <a class="rsswidget" href="http://feedproxy.googl ...
```

```r
blogs_list <- blogs_list[str_detect(blogs_list, "xoxo blogroll")][2]
blogs_list
```

```
## {xml_nodeset (1)}
## [1] <ul class="xoxo blogroll"><li><a href="#"></a></li>\n<li><a href="#" ...
```

```r
blogs_list <- toString(blogs_list)
blogs_list <- str_split(blogs_list, "<li><a", simplify = TRUE)
# it starts from 6
blogs_list <- blogs_list[6:length(blogs_list)]
head(blogs_list)
```

```
## [1] " href=\"http://dmlc.ml\" onclick=\"_gaq.push(['_trackEvent', 'outbound-widget', 'http://dmlc.ml', ' R stats']);\"> R stats</a></li>\n"                                                                                         
## [2] " href=\"http://lionel-.github.io\" onclick=\"_gaq.push(['_trackEvent', 'outbound-widget', 'http://lionel-.github.io', ' rstats']);\"> rstats</a></li>\n"                                                                       
## [3] " href=\"https://jean9208.github.io/rss-R.xml\" onclick=\"_gaq.push(['_trackEvent', 'outbound-widget', 'https://jean9208.github.io/rss-R.xml', 'Jean Arreola']);\">Jean Arreola</a></li>\n"                                   
## [4] " href=\"http://ryouready.wordpress.com\" onclick=\"_gaq.push(['_trackEvent', 'outbound-widget', 'http://ryouready.wordpress.com', 'R you ready?']);\" title=\"My advances in R  a learners diary\">R you ready?</a></li>\n"
## [5] " href=\"https://rveryday.wordpress.com/\" onclick=\"_gaq.push(['_trackEvent', 'outbound-widget', 'https://rveryday.wordpress.com/', '(R)very Day']);\">(R)very Day</a></li>\n"                                                   
## [6] " href=\"http://www.talyarkoni.org/blog\" onclick=\"_gaq.push(['_trackEvent', 'outbound-widget', 'http://www.talyarkoni.org/blog', '[citation needed] » R']);\">[citation needed] » R</a></li>\n"
```

OK so now from each element of `blogs_list` I want to extract the name of the blog, e.g. '– R stats', and its address, e.g. 'http://dmlc.ml'. I'll first extract the links because using the link it'll be easier to extract the blog name. Note the use of `map_chr`: using this variant of `purrr` function `map`, you directly get a `character` vector instead of a list.


```r
extract_link <- function(x){
  x <- str_replace(x, 
                   pattern = "href=\\\"",
                   replacement = "")
  x <- str_replace(x, 
                   pattern = "\\\" onclick=.*\\n",
                   replacement = "")
  x <- str_trim(x)
  return(x)
}

blogs_address <- map_chr(blogs_list, extract_link)
# the last one is a special snowflake
blogs_address[length(blogs_address)] <- str_replace(blogs_address[length(blogs_address)],
                                                    "\\\n\\\t<\\/ul>", "")

head(blogs_address)
```

```
## [1] "http://dmlc.ml"                      
## [2] "http://lionel-.github.io"            
## [3] "https://jean9208.github.io/rss-R.xml"
## [4] "http://ryouready.wordpress.com"      
## [5] "https://rveryday.wordpress.com/"     
## [6] "http://www.talyarkoni.org/blog"
```

So let's now extract names!


```r
extract_names <- function(x, address){
  address <- str_replace_all(address, "\\/", "\\\\\\/")
  address <- str_replace_all(address, "\\.", "\\\\\\.")
  x <- str_replace_all(x, paste0(".*", address, "', '"), "")
  x <- str_replace_all(x, "]\\)\\;.*\\\n", "")
  x <- str_replace(x, "'", "")
  return(x)
}

blogs_names <- map2_chr(blogs_list, blogs_address, extract_names)
# last one is a special snowflake again
blogs_names[length(blogs_names)] <- str_replace(blogs_names[length(blogs_address)],
                                                    "\\\n\\\t<\\/ul>", "")
head(blogs_names)
```

```
## [1] " R stats"             " rstats"              "Jean Arreola"       
## [4] "R you ready?"        "(R)very Day"           "[citation needed] » R"
```

Now I can build a `tibble` of names and addresses of blogs!



```r
blogs_info <- tibble(name = blogs_names,
                     address = blogs_address)
```

There are 746 blogs. The list also includes blogs that are no longer updated apparently, but that contributed content to R-bloggers at one point. My own blog isn't in the list yet which is fine because "Maëlle" is a pretty unique blog name but neither funny nor witty, except if testing encoding issues is funny or witty.

Before starting to have fun looking at blog names and addresses, I'll extract the extension from the addresses.


```r
extract_extension <- function(df){
  x <- str_replace(df$address, "feed\\.r-bloggers\\.xml", "")
  x <- str_replace(x, "\\.xml", "")
  x <- str_replace(x, "\\.html", "")
  x <- str_replace(x, "\\.php", "")
  x <- str_split(x, "\\.", simplify = TRUE)
  x <- x[length(x)]
  x <- str_replace_all(x, "\\/.*", "")
  return(x)
}

blogs_info <- by_row(blogs_info,
                     extract_extension,
                     .to = "extension",
                     .collate = "cols")


knitr::kable(head(blogs_info, n = 20))
```



|name                                               |address                                                      |extension |
|:--------------------------------------------------|:------------------------------------------------------------|:---------|
|– R stats                                          |http://dmlc.ml                                               |ml        |
|– rstats                                           |http://lionel-.github.io                                     |io        |
|–Jean Arreola–                                     |https://jean9208.github.io/rss-R.xml                         |io        |
|“R” you ready?                                     |http://ryouready.wordpress.com                               |com       |
|(R)very Day                                        |https://rveryday.wordpress.com/                              |com       |
|[citation needed] » R                              |http://www.talyarkoni.org/blog                               |org       |
|[R] tricks                                         |http://rtricks.wordpress.com                                 |com       |
|/mgritts_                                          |http://mgritts.com/feed.r.xml                                |r         |
|#untitled                                          |http://blog.ambodi.com/                                      |com       |
|0xCAFEBABE                                         |http://xcafebabe.blogspot.com/search/label/R                 |com       |
|4D Pie Charts » R                                  |http://4dpiecharts.com                                       |com       |
|56north &#124; Skræddersyet dataanalyse » Renglish |http://www.56n.dk                                            |dk        |
|A blog from Sydney                                 |http://a-blog-from-sydney.blogspot.com/search/label/R        |com       |
|A Blog On Data Analytics                           |http://aliarsalankazmi.github.io/blog_DA/                    |io        |
|A HopStat and Jump Away » Rbloggers                |http://hopstat.wordpress.com/                                |com       |
|a Physicist in Wall Street                         |http://aphysicistinwallstreet.blogspot.com/search/label/R    |com       |
|A Pint of R                                        |http://rpint.wordpress.com                                   |com       |
|A Statistics Blog – R                              |http://statistics.rainandrhino.org/R.xml                     |org       |
|Actuarially (Matt Malin)                           |http://www.actuarially.co.uk/                                |uk        |
|Adventures in Analytics and Visualization          |http://analyticsandvisualization.blogspot.com/search/label/R |com       |

# Analysing blog info

## How and where do people blog?

Out of the 746 blogs there are 156 `blogspot.com` blogs, 181 `wordpress.com` blogs and  53 `github.io` blogs. I guess most `github.io` blogs are [`knitr-jekyll`](https://github.com/yihui/knitr-jekyll)-based like mine, or more modern [`blogdown`](https://github.com/rstudio/blogdown) blogs. For blogs with a specific domain name, e.g. Julia Silge's excellent ["data science ish" blog](http://juliasilge.com) who has "juliasilge" as domain name, it's more difficult to know how people blog. Well I know Julia uses `knitr-jekyll`.

What about extensions?


```r
library("dplyr")
blogs_info %>% group_by(extension) %>%
  summarize(n = n()) %>%
  arrange(desc(n)) %>%
  head(n = 16) %>%
  knitr::kable()
```



|extension |   n|
|:---------|---:|
|com       | 533|
|io        |  53|
|org       |  39|
|net       |  27|
|de        |  11|
|info      |   7|
|me        |   7|
|uk        |   7|
|nl        |   6|
|co        |   4|
|eu        |   4|
|name      |   4|
|ca        |   3|
|edu       |   3|
|es        |   3|
|fr        |   3|

I totally stopped at "fr" on purpose. Without surprise given the number of blogspot/wordpress blogs ".com" is a favourite, and then ".io". I'm quite happy to see 4 fellow ".eu" bloggers. But I'll admit regretting I didn't choose ".guru" like [this blog](https://youranalytics.guru) because I'd have loved to become a R guru.

## So, what's in blog names?

The first thing I noticed when looking at the names of blogs was the high frequency of puns! There's the ["Hallway Mathlete"](http://www.hallwaymathlete.com/search/label/rblogger), ["Once Upon a Data"](http://omaymaS.github.io/), ["i’m a chordata! urochordata! » R"](http://www.imachordata.com) (too bad this website doesn't seem to exist any longer), etc. And well these were regular data / statistics puns... then we have the R puns! ["Shirin’s playgRound"](https://shiring.github.io/), ["“R” you ready?"](http://ryouready.wordpress.com), ["(R)very Day"](https://rveryday.wordpress.com/)... R Bloggers are a bit like hair dressers, said my husband when I told him about some names.

Another not surprising feature of some blog names is to include something about statistics, like David Robinson's ["Variance Explained"](http://varianceexplained.org). Maybe we could resolve the Bayesians vs. frequentist feud by looking at blog names? Well 4 have a name including "Bayes", 0 a "frequentist" name. That said, I can have missed a pun indicating the author's position on the subject.

Names of blogs can also indicate the field where the author applies R, e.g. 15 blogs include something "bio"-ish and 3 contain the word "finance".

Also, in case you want a bit of action in your life, there are 6 "adventures" blogs, and 3 "journey" blogs. By the way, my PhD advisor's blog, ["Theory meets practice…"](http://staff.math.su.se/hoehle/blog/) can also help you in your daily adventures, from [choosing a Tinder profile](http://staff.math.su.se/hoehle/blog/2016/06/12/optimalChoice.html) to [knowing which Kinder Surprise eggs contain a figure](http://staff.math.su.se/hoehle/blog/2016/12/23/surprise.html).

After this random exploration of blog names, I realized you'd all be waiting for a wordcloud. Or so I think. So let's make a wordcloud of R blog names!


```r
library("wordcloud")
words <- toString(blogs_info$name)
words <- str_split(words, pattern = " ", simplify = TRUE)
set.seed(1)
wordcloud(words, colors = viridis::viridis_pal(end = 0.8)(10),
          min.freq = 3, random.color = TRUE)
```

![plot of chunk unnamed-chunk-7](/figure/source/2017-01-29-rbloggersnames/unnamed-chunk-7-1.png)

# Conclusion

You might be disappointed that I don't have any good advice for naming your blog. Sure, looking at existing names you can choose one for fitting in (including words like "data", "rstats", etc.) or a very original one, but does it predict success? I guess it's better, however, to have a really cool blog name if you start printing t-shirts for your readers.

Another thing you might need to name during your R journey/adventures is a package. If you're interested in this issue, you might want to read Alex Whant's post ["What's in a [package] name?"](http://alexwhan.com/2016-04-19-package-names).

And last but not least, thanks again to Tal for creating and maintaining R-bloggers!
