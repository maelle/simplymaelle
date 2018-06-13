---
title: A tribute to Lucy D'Agostino McGowan's git commit emoji game
tags:
  - GitHub-API-V3
  - GitHub
  - GitHub-API
  - gh
date: '2017-05-03'
slug: lucysemojis
comments: yes
---


Do you know [Lucy](https://twitter.com/LucyStats)? She is a very talented biostatistics PhD candidate that I had the chance to e-meet thanks to R-Ladies. One maybe superficial reason to admire her, on top of her other achievements, is her emoji game in git commits. Looking at Lucy's git history (find [her on Github](https://github.com/LucyMcGowan)), one wants to start using version control because she makes it look fun!

In this post, I will download many git commit messages of Lucy's from Github's API via the `gh` package, and have a look at the emojis she uses the most frequently.


<!--more-->

# Getting the commit messages

As I said I'll use the `gh` package as in my post about [first commits](/2017/02/21/firstcommit/) and as in the one about [random seeds](/2017/04/12/seeds/).

The first step is to get the names of Lucy's repositories. I get all her public repositories, and also a few of the repositories of the LFOD organization [where she blogs](http://livefreeordichotomize.com/), that I selected by hand for not getting forks from the public repositories I already had. Hopefully her commit messages in these repositories, obviously excluding her private repositories, is representative of her typical emoji use.


```r
library("gh")
library("magrittr")
lucy_repos <- gh("/users/:username/repos", 
                 type = "public", 
                 username = "LucyMcGowan")
lucy_repos <- vapply(lucy_repos, "[[", "", "name")
lucy_repos <- tibble::tibble(repos = lucy_repos,
                             who = "LucyMcGowan")
lucy_repos_org <- tibble::tibble(repos = c("LFOD.github.io",
                                           "blog",
                                           "real-blog"),
                                 who = "LFOD")

lucy_repos <- dplyr::bind_rows(lucy_repos,
                               lucy_repos_org)

head(lucy_repos) %>%
  knitr::kable()
```



|repos                |who         |
|:--------------------|:-----------|
|2017-03-18_hackathon |LucyMcGowan |
|branchingExample     |LucyMcGowan |
|comps-survival-guide |LucyMcGowan |
|contributr           |LucyMcGowan |
|cowsay               |LucyMcGowan |
|datasauRus           |LucyMcGowan |

Then I got the commit messages, but also their authors in order to filter commits by Lucy herself, and the time and date at which they were made because Lucy told me she started using emojis last October. `gh` is a minimal package so it outputs lists of lists, so each time I use it I have a look at their structure to find how to extract exactly what I need, see below.


```r
get_all_lucy_commits <- function(repo, who){
  
  # Since I don't know the number of pages
  # I grow the vectors of messages, dates and authors
  messages <- NULL
  authors <- NULL
  dates <- NULL
  
  page <- 1
  is_still_ok <- TRUE
  # loop while page still full of content
  while(is_still_ok[1]){
    commits <- try(gh("/repos/:owner/:repo/commits",
                      owner = who,
                      repo = repo,
                      page = page))
    
    if(class(commits)[1] != "try-error"){
      is_still_ok <- commits != ""
    }else{
      is_still_ok <- FALSE
    }
    
    if(is_still_ok[1]){
      # get information about commits
      commit_info <- lapply(commits, "[[", "commit")
      
      # commit messages
      now_messages <- lapply(commit_info, "[[", "message")
      messages <- c(messages, unlist(now_messages))
      
      # date (and time)
      committer <- lapply(commit_info, "[[", "committer")
      now_dates <- lapply(committer, "[[", "date")
      dates <- c(dates, unlist(now_dates))
      
      # who committed
      now_authors <- lapply(commit_info, "[[", "author")
      now_authors <- lapply(now_authors, "[[", "name")
      authors <- c(authors, unlist(now_authors))
      
      page <- page + 1
    }
  }
  
  results <- tibble::tibble(commit = messages,
                           repo = repo,
                           owner = who,
                           author = authors,
                           datetime = dates)
  # I only want commits from Lucy herself
  dplyr::filter_(results, 
                 lazyeval::interp(~ stringr::str_detect(author, "[Ll]ucy")))
}

commits <- purrr::map2(lucy_repos$repos,
                       lucy_repos$who, 
                       get_all_lucy_commits)
commits <- dplyr::bind_rows(commits)
commits <- dplyr::mutate(commits, 
                         encoding = Encoding(commit))

commits <- dplyr::mutate(commits, 
                         datetime = lubridate::ymd_hms(datetime))


head(commits) %>%
  knitr::kable()
```



|commit                                          |repo                 |owner       |author                  |datetime            |encoding |
|:-----------------------------------------------|:--------------------|:-----------|:-----------------------|:-------------------|:--------|
|Rename park_dat_json.JSON to park_dat_json.json |2017-03-18_hackathon |LucyMcGowan |Lucy D'Agostino McGowan |2017-03-18 19:59:44 |unknown  |
|update json                                     |2017-03-18_hackathon |LucyMcGowan |Lucy                    |2017-03-18 19:59:00 |unknown  |
|update json                                     |2017-03-18_hackathon |LucyMcGowan |Lucy                    |2017-03-18 19:57:29 |unknown  |
|update json                                     |2017-03-18_hackathon |LucyMcGowan |Lucy                    |2017-03-18 19:57:16 |unknown  |
|fix json                                        |2017-03-18_hackathon |LucyMcGowan |Lucy                    |2017-03-18 19:31:56 |unknown  |
|first commit <f0><U+009F><U+00A4><U+00A1>       |2017-03-18_hackathon |LucyMcGowan |Lucy                    |2017-03-18 19:23:06 |UTF-8    |

# Looking at emojis

I used this [awesome emoji dictionary](https://github.com/today-is-a-good-day/emojis/blob/master/emDict.csv) created and presented in [this blog](http://opiateforthemass.es/articles/emoticons-in-R/).


```r
dico <- readr::read_csv2("https://raw.githubusercontent.com/today-is-a-good-day/emojis/master/emDict.csv")
head(dico) %>%
  knitr::kable()
```



|Description       |Native                       |Bytes            |R-encoding               |
|:-----------------|:----------------------------|:----------------|:------------------------|
|AERIAL TRAMWAY    |<f0><U+009F><U+009A><U+00A1> |\xF0\x9F\x9A\xA1 |<ed><a0><bd><ed><ba><a1> |
|AIRPLANE          |<U+2708>                     |\xE2\x9C\x88     |<e2><9c><88>             |
|ALARM CLOCK       |<U+23F0>                     |\xE2\x8F\xB0     |<e2><8f><b0>             |
|ALIEN MONSTER     |<f0><U+009F><U+0091><U+00BE> |\xF0\x9F\x91\xBE |<ed><a0><bd><ed><b1><be> |
|AMBULANCE         |<f0><U+009F><U+009A><U+0091> |\xF0\x9F\x9A\x91 |<ed><a0><bd><ed><ba><91> |
|AMERICAN FOOTBALL |<f0><U+009F><U+008F><U+0088> |\xF0\x9F\x8F\x88 |<ed><a0><bc><ed><bf><88> |

As the Github username of the person who created, today is a good day! Like in [my last post](/2017/04/30/radioedit/) I'll use `fuzzyjoin` to identify emojis.


```r
library("fuzzyjoin")

commits_decoded <- regex_inner_join(commits, dico,
                            by = c(commit = "Native"))
```

Yes! Doing this, I got 63 emojis!


```r
table(commits_decoded$Description) %>%
  broom::tidy() %>%
  dplyr::arrange(desc(Freq)) %>%
  head(n = 10) %>%
  knitr::kable()
```



|Var1                    | Freq|
|:-----------------------|----:|
|SEE-NO-EVIL MONKEY      |   10|
|ROOSTER                 |    8|
|BUG                     |    4|
|FACE WITH OK GESTURE    |    4|
|NAIL POLISH             |    3|
|PARTY POPPER            |    3|
|FACE SCREAMING IN FEAR  |    2|
|INFORMATION DESK PERSON |    2|
|RUNNER                  |    2|
|SUNFLOWER               |    2|

Seeing the frequency of the "see no evil" emoji... Is Lucy ashamed of her commits? Why, Lucy? Then, rooster is a chicken, and Lucy is trying [to make the "#RChickenLadies" hashtag happen](https://twitter.com/LucyStats/status/829905505862709249) (note to self: Lucy probably has puns in her commit messages). One important point though, on top of these cool most frequent emojis, is the diversity of emojis, 33 unique emojis for 63 emojis in total. It's a real language! I can't wait for Lucy to commit and push more in her public repositories to try and decode this language! And also to see the awesome work behind each commit, of course.
