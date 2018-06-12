---
title: First commit or initial commit?
date: '2017-02-21'
slug: firstcommit
comments: yes
---


When I create a new .git repository, my first commit message tends to be "1st commit". I've been wondering what other people use as initial commit message. Today I used the [`gh` package](https://github.com/r-pkgs/gh) to get first commits of all repositories of the [ropensci](https://github.com/ropensci) and [ropenscilabs](https://github.com/ropenscilabs) organizations.

<!--more-->

The sample might seem a bit small, but I just wanted to start exploring my question. I agree that it means my answer won't be very conclusive.

# Getting all repos for an organization

I've come up with a quite inelegant solution to paging, I just continue querying the API until it returns me nothing.


```r
library("gh")
library("dplyr")
library("purrr")
get_repos <- function(org){
  ropensci_repos_names <- NULL
page <- 1
geht <- TRUE
while(geht){
  ropensci_repos <- try(gh("/orgs/:org/repos",
                  org = org,
                  page = page))
  
  geht <- ropensci_repos != ""
  
  if(geht){
    ropensci_repos_names <- c(ropensci_repos_names,
                              vapply(ropensci_repos, "[[", "", "name"))
    page <- page + 1
  }
}
  return(ropensci_repos_names)
}

head(get_repos(org = "ropenscilabs"))
```

```
## [1] "webmockr"      "vcr"           "seasl"         "plater"       
## [5] "rnaturalearth" "convertr"
```

# Get first commit for a repository

Here I'm doing something quite inefficient. Since the API returns the most recent commits first I get all commits. I could have used the creation date of the repository instead to only query commits created shortly after that.


```r
first_commit <- function(repo, org){
  messages <- NULL
  
  page <- 1
  geht <- TRUE
  while(geht){
    commits <- try(gh("/repos/:owner/:repo/commits",
                            owner = org,
                            repo = repo,
                            page = page))
    
    if(class(commits)[1] != "try-error"){
      geht <- commits != ""
    }else{
      geht <- FALSE
    }
    
    if(geht){
      now <- lapply(commits, "[[", "commit")
      now <- lapply(now, "[[", "message")
      messages <- c(messages, unlist(now))
      page <- page + 1
    }
  }
  
  messages[length(messages)]
}
first_commit("ropenaq", "ropensci")
```

```
## [1] "Everything"
```

I'm a bit surprised I chose "Everything" as first commit for my `ropenaq` package, actually. Not because I expect my commit history to be particularly smart either, just because it's not a "1st commit".

# Get all the first commits


```r
first_commits <- get_repos("ropenscilabs") %>%
  map(first_commit, org = "ropenscilabs") 
save(first_commits, file = "data/2017-02-21_ropenscilabs_first_commits.RData")
first_commits <- get_repos("ropensci") %>%
  map(first_commit, org = "ropensci") 
save(first_commits, file = "data/2017-02-21_ropensci_first_commits.RData")
```

# What are the most frequent first commits?


```r
load("data/2017-02-21_ropenscilabs_first_commits.RData")
ropenscilabs <- first_commits
load("data/2017-02-21_ropensci_first_commits.RData")
ropensci <- first_commits

all <- c(unlist(ropenscilabs),
         unlist(ropensci))
firstc <- tibble::tibble(commit = all)
firstc <- mutate(firstc, commit = tolower(commit))
firstc %>%
  group_by(commit) %>%
  summarize(n = n()) %>%
  arrange(desc(n)) %>%
  head(n = 15) %>%
  knitr::kable()
```



|commit                              |   n|
|:-----------------------------------|---:|
|first commit                        | 117|
|initial commit                      |  76|
|added readme                        |  19|
|added files                         |   9|
|1st commit                          |   3|
|create readme.md                    |   3|
|init                                |   3|
|added readme file                   |   2|
|code extracted from mikabr/devtools |   2|
|first comit                         |   2|
|first commit, added files           |   2|
|initial                             |   2|
|initial import                      |   2|
|package infrastructure              |   2|
|rstudio new package project         |   2|


Out of the 362 repositories, 76 used "initial commit" as a first commit message and  117 used "first commit" instead. In total 0.53 of all repos used either one of these two messages, which isn't as much as I expected. But maybe rOpenSci repositories are unusual as regards first commit originality? And you, what is your favourite initial commit message if you have one?
