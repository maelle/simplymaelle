---
title: Extracting notable deaths from Wikipedia
date: '2017-02-12'
slug: wikideaths3_scraping
comments: yes
---



I like Wikipedia. My husband likes it even more, he included it in his PhD thesis acknowledgements! I appreciate the efforts done for sharing knowledge, and also the apparently random stuff you can find on the website. In particular, I've been intrigued by the monthly lists of notable deaths such as [this one](https://en.wikipedia.org/wiki/Deaths_in_September_2011). Who are people (or dogs, yes, dogs) whose life was deemed notable enough to be listed there? Also, using the numbers of such deaths, can I judge whether 2016 was really worse than previous years? The first step in answering these questions was to scrape the data. I'll describe the process in this post. [In another post](/2017/02/12/wikideaths2_population/) I'll have a look at my study population and [in a third post](/2017/02/12/wikideaths1_ts/) I'll analyse the time series of death counts.

<!--more-->

I have extracted all deaths listed in Wikipedia from 2004 to 2016 from monthly pages. For most deaths, I extracted the Wikipedia link, the name of the person, their age and the first part of their presentation, which most often includes a nationality and reason for being famous, e.g. "Italian astrophysicist". I chose not to get the rest of the line if it was longer, because the length of the reason for being famous was quite variable and cause of death was not consistently indicated.

Note that there are list of notable deaths for deaths occurred before 2004 but they are in a different format so let's say it's a challenge for another day (or another person, like you, dear reader?).

## Downloading the lists of deaths

I started by downloading the content of all monthly lists of deaths from 2004 to 2016.


```r
library("rvest")
library("dplyr")
library("purrr")
library("tidyr")
library("lazyeval")
library("tibble")
library("fuzzyjoin")
library("stringr")
months <- c("January", "February",
            "March", "April",
            "May", "June",
            "July", "August",
            "September", "October",
            "November", "December")
years <- 2004:2016
pages_content <- map(months, paste, years, sep = "_") %>%
  unlist() %>%
  # read page with monthly deaths
  map(function(x){
    read_html(paste0("https://en.wikipedia.org/wiki/Deaths_in_", x))})
```

## Transforming the content of the lists into a table

I actually did all this stuff months ago. At that time apparently I was patient enough to figure out how to extract information from the list. I was surprised to see my code still worked, for getting all of 2016 I just needed to change 2015 into 2016. Note that I use the name list, it's a list on the webpage, not a table, which means I couldn't just rely on `rvest` and take a nap. My main goal was to be able to get something nice for _typical entries_ and just forget about the non-typical entries, hoping there wouldn't be many. Here are the two functions I defined. I use `stringr`, not `stringi`, because it's an old code where this was enough but since reading [Bob Rudis' post](https://rud.is/b/2017/02/06/strung-out-on-string-ops-a-brief-comparison-of-stringi-and-stringr/) I am more curious about `stringi`.


```r
transform_day <- function(day_deaths, day_in_month){
  # filter only those that have the format of lines presenting deaths
  day_deaths <- str_replace_all(day_deaths$days, "<ul><li>", "")
  day_deaths <- str_split(day_deaths, "<li>", simplify = TRUE)
  day_deaths <- day_deaths[!str_detect(day_deaths, "Death")]
  day_deaths <- day_deaths[!str_detect(day_deaths, "Category")]
  
  # Erases the end of each line
  day_deaths <- str_replace_all(day_deaths, "\\(<i>.*", "")
  
  # Create a table
  day_deaths <- tibble_(list(line = ~day_deaths))
  
  # Variable for grouping by row 
  day_deaths <- mutate_(day_deaths, row = interp(~1:nrow(day_deaths)))
  day_deaths <- group_by_(day_deaths, "row")
  
  # separate by the word "title", first part is a link, second part description
  day_deaths <- mutate_(day_deaths, line = interp(~str_split(line, "title")))
  day_deaths <- mutate_(day_deaths, wiki_link = interp(~str_replace_all(line[[1]][1], "<a href=\\\"/wiki/", "")))
  
  # get Wikipedia link or better said the thing to paste to Wikipedia address
  day_deaths <- mutate_(day_deaths, 
                        wiki_link = interp(~str_replace_all(wiki_link, "\\\\", "")))
  day_deaths <- mutate_(day_deaths, 
                        wiki_link = interp(~str_replace_all(wiki_link, "\"", "")))
  
  
  # now transform the description into several columns
  # the format of the end of the description is variable so
  # I only keep the beginning of the reason for notoriety i.e. country of origin
  # and a role
  # anyway cause of death is not written for all and I don't want to use many details
  day_deaths <- mutate_(day_deaths, content = interp(~line[[1]][2]))
  day_deaths <- mutate_(day_deaths,
                        content = interp(~str_replace_all(content, '<a.*a>', "")))
  day_deaths <- separate_(day_deaths, "content", 
                          into = c("name", "age", "country_role"),
                          sep = ",")
  
  # when no age
  day_deaths <- mutate_(day_deaths, country_role = interp(~ifelse(is.na(country_role),
                                                                  age,
                                                                  country_role)))
  day_deaths <- mutate_(day_deaths, age = interp(~ as.numeric(age)))
  
  # improves the name
  day_deaths <- mutate_(day_deaths, 
                        name = interp(~ str_replace_all(name, "\">.*", "")))
  day_deaths <- mutate_(day_deaths, 
                        name = interp(~ str_replace_all(name, "=\"", "")))
  
  # improves the country_role
  day_deaths <- mutate_(day_deaths, 
                        country_role = interp(~ str_replace_all(country_role, "</li>", "")))
  day_deaths <- mutate_(day_deaths, 
                        country_role = interp(~ str_replace_all
                                              (country_role, "</ul>", "")))
  
  # get rid of original line
  day_deaths <- select_(day_deaths, quote(- line))
  
  # get rid of grouping
  day_deaths <- ungroup(day_deaths)
  day_deaths <- select_(day_deaths, quote(- row))
  return(day_deaths)
}

parse_month_deaths <- function(month_deaths){
  
  # remember month and year
  title <- stringr::str_extract(toString(html_nodes(month_deaths, "title")), 
                                "Deaths in .* -")
  
  title <- str_replace_all(title, "Deaths in ", "")
  title <- str_replace_all(title, " -", "")
  title <- str_replace_all(title, "January", "01")
  title <- str_replace_all(title, "February", "02")
  title <- str_replace_all(title, "March", "03")
  title <- str_replace_all(title, "April", "04")
  title <- str_replace_all(title, "May", "05")
  title <- str_replace_all(title, "June", "06")
  title <- str_replace_all(title, "July", "07")
  title <- str_replace_all(title, "August", "08")
  title <- str_replace_all(title, "September", "09")
  title <- str_replace_all(title, "October", "10")
  title <- str_replace_all(title, "November", "11")
  title <- str_replace_all(title, "December", "12")
  
  # find days with deaths
  content <- toString(month_deaths)
  content <- str_split(content, "\n", simplify = TRUE)
  days <- which(str_detect(content, "h3"))
  
  paragraphs <- which(str_detect(content, "<ul>"))
  
  last_good <- max(which(str_detect(unlist(lapply(html_nodes(month_deaths, "h3"), 
                                             toString)),
                               pattern = "title=Deaths_in_.*_..")))
  
  possible_days <- 1:last_good
  
  possible_days <- possible_days[diff(c(days[possible_days], 999999)) > 1]
  # read only lines
  month_deaths <- html_nodes(month_deaths, "ul")
  first_not_good <- max(which(str_detect(month_deaths, "Template")))
  first_good <- min(which(str_detect(month_deaths, "wiki")))
  paragraphs <- paragraphs[first_good:(first_not_good - 1)]
  
  if(length(paragraphs) > length(possible_days)){
    jours <- NULL
    for(paragraph in paragraphs){
      x <- days[days < paragraph]
      jours <- c(jours,
                 possible_days[which(abs(x-paragraph)==min(abs(x-paragraph)))])
    }
    possible_days <- jours
  }
  
  month_deaths <- month_deaths[first_good:(first_not_good - 1)]
  month_deaths <- unlist(map(month_deaths, toString))
  
  
  
  # transform for getting the different columns
  month_deaths_table <- data.frame(days = month_deaths,
                                   day_in_month = possible_days,
                                   title = title) %>%
    by_row(transform_day, .to = "all_deaths") %>%
    unnest_("all_deaths") %>%
    group_by_("wiki_link") %>%
    mutate_(index = ~ 1:n()) %>%
    filter_(~index == 1) %>%
    select_(quote(-index)) %>%
    group_by_("days") %>%
    mutate_(date = interp(~ lubridate::dmy(paste(day_in_month, title)))) %>%
    select_(quote(- day_in_month)) %>%
    select_(quote(- title)) %>%
    ungroup() %>%
    select_(quote(- days)) 
  
  month_deaths_table
  
}
```


Once the functions were written (and tested on a few pages) I simply mapped them to all the pages.


```r
deaths_2004_2016 <- pages_content %>%  
  map(parse_month_deaths) %>%
  dplyr::bind_rows()
knitr::kable(head(deaths_2004_2016))
```



|wiki_link                    |name               | age|country_role                                                       |date       |
|:----------------------------|:------------------|---:|:------------------------------------------------------------------|:----------|
|Harold_Henning               |Harold Henning     |  69|South African golfer.                                              |2004-01-01 |
|Elma_Lewis                   |Elma Lewis         |  82|American arts leader.                                              |2004-01-01 |
|Manuel_F%C3%A9lix_L%C3%B3pez |Manuel Félix López |  66|Ecuadorian politician.                                             |2004-01-01 |
|Frederick_Redlich            |Frederick Redlich  |  93|Austrian-born American dean of the <a href="/wiki/Yale_University" |2004-01-01 |
|Etta_Moten_Barnett           |Etta Moten Barnett | 102|American actress.                                                  |2004-01-02 |
|Lynn_Cartwright              |Lynn Cartwright    |  76|U.S. actress.                                                      |2004-01-02 |

I was already quite happy to get this table, but I wanted to add a country to most rows, and separate the role of the person from the adjectival.

## Get the demonyms table from Wikipedia

I discovered Wikipedia has a [table](https://en.m.wikipedia.org/wiki/List_of_adjectival_and_demonymic_forms_for_countries_and_nations) (a table! a table!) of adjectivals for many countries and nations. The only thing I changed was getting one line by adjectivals when there were several ones by country or nation. I also calculated the number of words in this adjectival in order to be able to easily remove it from the `"country_role"` column and thus get the role on its own.


```r
demonyms_page <- read_html("https://en.m.wikipedia.org/wiki/List_of_adjectival_and_demonymic_forms_for_countries_and_nations")

demonyms_table <- html_nodes(demonyms_page, "table")[1]
demonyms_table <- html_table(demonyms_table, fill = TRUE)[[1]]
names(demonyms_table) <- c("country", "adjectivals", "demonyms", "colloquial_demonyms")
demonyms_table <- tibble::as_tibble(demonyms_table)
demonyms_table <- by_row(demonyms_table,
                         function(df){
                           adjectivals <- tibble_(list(goodadjectivals = interp(~str_split(df$adjectivals, " or "))))
                           }, .collate = "list", .to = "good_adjectivals") %>%
  unnest_("good_adjectivals") %>%
  unnest_("goodadjectivals")
demonyms_table <- select_(demonyms_table, quote(- adjectivals))
demonyms_table <- rename_(demonyms_table, "adjectivals" = "goodadjectivals" )
demonyms_table <- by_row(demonyms_table,
                         function(df){
                           adjectivals <- tibble_(list(goodadjectivals = interp(~str_split(df$adjectivals, ","))))
                         }, .collate = "list", .to = "good_adjectivals") %>%
  unnest_("good_adjectivals") %>%
  unnest_("goodadjectivals")

demonyms_table <- select_(demonyms_table, quote(- adjectivals))
demonyms_table <- rename_(demonyms_table, "adjectivals" = "goodadjectivals" )

demonyms_table <- mutate(demonyms_table, adjectivals = trimws(adjectivals ))
demonyms_table <- by_row(demonyms_table,
                         function(df){
                           length(str_split(df$adjectivals, " ")[[1]])},
                         .to = "adj_length", .collate = "cols")
```

## Using adjectivals to split deaths' country and role

For finding which country/nation to add to a line I used `fuzzyjoin::regex_left_join()` which worked well but a bit slowly given the number of lines.


```r
deaths <- deaths_2004_2016
demonyms <- demonyms_table
deaths <- mutate(deaths, country_role = str_replace(country_role,
                                                    "<.*", ""))

demonyms <- mutate(demonyms, adjectivals = strsplit(adjectivals, ","))
demonyms <- unnest(demonyms, adjectivals)
demonyms <- mutate(demonyms, 
                   adjectivals = paste0(adjectivals, " .*"))
deaths <- regex_left_join(deaths, 
                          demonyms, by = c("country_role" = "adjectivals"))

deaths <- mutate(deaths,
                 country = ifelse(str_detect(country_role, "American"),
                                  "United States",
                                  country))

deaths <- mutate(deaths,
                 adjectivals = ifelse(country == "United States",
                                       "American", adjectivals))
deaths <- mutate(deaths,
                 adj_length = ifelse(country == "United States",
                                      1, adj_length))
# keep one country only
deaths <- group_by(deaths, wiki_link) %>%
  mutate(index = 1:n()) %>%
  filter(index == 1)
```



```r
deaths <- by_row(deaths,
                 function(df){
                   if(!is.na(df$adj_length)) {
                     country_role <- trimws(df$country_role)
                     splitted <- str_split(country_role, " ")[[1]]
                     role <- toString(splitted[(df$adj_length + 1):length(splitted)])
                     str_replace_all(role, ",", "")
                     }else{
                     df$country_role
                   }
                   
                 }, .to = "occupation", .collate = "cols")

deaths <- mutate(deaths, 
                 occupation = str_replace_all(occupation, "\\r", ""))
deaths <- mutate(deaths, 
                 occupation = str_replace_all(occupation, "\\n", ""))
deaths <- mutate(deaths, 
                 occupation = str_replace_all(occupation, "\\.", ""))

deaths <- select(deaths, - demonyms, - colloquial_demonyms,
                   - index)
readr::write_csv(deaths, path = "data/deaths_with_demonyms.csv")
knitr::kable(head(deaths))
```



|wiki_link                    |name               | age|country_role                       |date       |country           | adj_length|adjectivals      |occupation           |
|:----------------------------|:------------------|---:|:----------------------------------|:----------|:-----------------|----------:|:----------------|:--------------------|
|Harold_Henning               |Harold Henning     |  69|South African golfer.              |2004-01-01 |South Africa      |          2|South African .* |golfer               |
|Elma_Lewis                   |Elma Lewis         |  82|American arts leader.              |2004-01-01 |United States     |          1|American         |arts leader          |
|Manuel_F%C3%A9lix_L%C3%B3pez |Manuel Félix López |  66|Ecuadorian politician.             |2004-01-01 |Ecuador           |          1|Ecuadorian .*    |politician           |
|Frederick_Redlich            |Frederick Redlich  |  93|Austrian-born American dean of the |2004-01-01 |United States     |          1|American         |American dean of the |
|Etta_Moten_Barnett           |Etta Moten Barnett | 102|American actress.                  |2004-01-02 |United States     |          1|American         |actress              |
|Lynn_Cartwright              |Lynn Cartwright    |  76|U.S. actress.                      |2004-01-02 |United States[20] |          1|U.S. .*          |actress              |

In the table I have information about 56303 notable deaths. I know the age of 97% of them, a country or nation for 96.2% of them. Not too bad I think! It was then time to stop scraping webpages and to start digging into the data... [Who were these people?](/2017/02/12/wikideaths2_population/) and [How bad was 2016?](/2017/02/12/wikideaths1_ts/)

I'd like to end this post with a note from my husband, who thinks having a blog makes me an influencer. If you too like Wikipedia, consider [donating to the Wikimedia foundation](https://wikimediafoundation.org/wiki/Ways_to_Give).
