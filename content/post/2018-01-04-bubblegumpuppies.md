---
title: Cheer up, Black Metal Cats! Bubblegum Puppies
date: '2018-01-04'
slug: 2018/01/04/bubblegumpuppies
comments: yes
tags: magick
---




Do you know the [Black Metal Cats Twitter account](https://twitter.com/evilbmcats)? As explained [in this great introduction](https://nerdist.com/cats-twitter-heavy-metal-lyrics/), it "combines kitties with heavy metal lyrics". I know the account because I follow [Scott Chamberlain](https://twitter.com/sckottie) who retweets them a lot, which I enjoy as far as one can enjoy such a dark mood. Speaking of which, I decided to try and transform Black Metal Cat tweets into something more positive... The [Bubblegum Puppies](https://twitter.com/cheerful_doggos) were born!

<!--more-->

# Getting Black Metal Cats tweets

It won't come as a surprise for the loyal readers of this blog that I just had to use `rtweet`. I kept only original standalone tweets and removed the picture link from the tweet.


```r
black_tweets <- rtweet::get_timeline("evilbmcats")
black_tweets <- dplyr::filter(black_tweets, is.na(reply_to_user_id), !is_retweet, !is_quote)
black_tweets <- dplyr::select(black_tweets, text, created_at, status_id)
black_tweets <- dplyr::mutate(black_tweets, text = stringr::str_replace(text, "https.*", ""))
readr::write_csv(black_tweets, path = "data/2018-01-03-bubblegumpuppies_cats.csv")
```


Now that the dark material is ready, let's sweeten it...

# Modifying the tweet text

Black Metal Cats tweet heavy metal lyrics so as you can imagine, they're sad. How to make them happy while keeping the text similar enough to the original one? And this without too much effort? My simplistic strategy was to identify negative words via sentiment analysis and to replace them with positive words. 

_Note, had I not wanted to stay close to the original tweet, I could have just chosen lyrics picked from [this dataset](https://www.rdocumentation.org/packages/billboard/versions/0.1.0/topics/lyrics) for instance, and filtered them by sentiment via the sentimentr package._

## Finding negative words

I computed sentiment using `tidytext` copy-pasting code from [this post of mine](http://www.masalmon.eu/2017/10/02/guardian-experience/).


```r
library("tidytext")
library("magrittr")
bing <- get_sentiments("bing")

sentiment <- dplyr::select(black_tweets, text) %>%
  dplyr::mutate(saved_text = text) %>%
  unnest_tokens(word, text) %>%
  dplyr::inner_join(bing, by = "word") %>%
  dplyr::filter(sentiment == "negative")
```

It was a bit disappointing since out of the 97 only 63 were represented in that data.frame. But well, this shall do! I just looked rapidly at some non included tweets.



```r
dplyr::filter(black_tweets, !text %in% sentiment$saved_text) %>%
  head() %>%
  knitr::kable()
```



|text                                            |created_at          |status_id          |
|:-----------------------------------------------|:-------------------|:------------------|
|Black covfefe cats.                             |2017-12-29 21:01:06 |946848575446663168 |
|Black coffee cats.                              |2017-12-28 00:15:48 |946172798203916288 |
|Say goodbye to the light.                       |2017-12-26 15:50:00 |945683121336344576 |
|The storm will never end.                       |2017-12-23 15:49:00 |944595705204678656 |
|Satan, your time has come. This world must end. |2017-12-18 22:36:00 |942886191107575808 |
|Timely.                                         |2017-12-13 00:43:30 |940743951287504896 |

Ok so some of them are probably negative tweets that the [`sentimentr` package](https://cran.r-project.org/web/packages/sentimentr/README.html) would help detect but that do not contain negative words.

## Replacing words

I seriously considered using the [`wordnet` package](https://cran.r-project.org/web/packages/wordnet/index.html) because of this [Stack Overflow question "Getting antonyms using the `wordnet` package"](https://stackoverflow.com/questions/19360107/getting-antonyms-using-the-r-wordnet-package) but I was not brave enough, my strength failed me in front of the Java needs of that package. 

I decided to use `praise` to get positive words, and `cleanNLP` (as in [this post](http://www.masalmon.eu/2017/12/05/badderb/)) to try and identify correctly negative words as adjective or verbs for instance in order to be able to replace them. The right annotation for that is a _token_. 



```r
library("cleanNLP")
init_spaCy()

get_token_with_text <- function(x){
  obj <- run_annotators(x, as_strings = TRUE)
  entities <- get_token(obj)
  entities$text <- x
  entities
}

possibly_get_tokens <- purrr::possibly(get_token_with_text,
                                         otherwise = NULL)

tokens <- purrr::map_df(sentiment$saved_text, possibly_get_tokens)
head(tokens) %>%
  knitr::kable()
```



| id| sid| tid|word      |lemma    |upos |pos | cid|text                           |
|--:|---:|---:|:---------|:--------|:----|:---|---:|:------------------------------|
|  1|   1|   1|We        |-PRON-   |PRON |PRP |   0|We are the creatures you fear. |
|  1|   1|   2|are       |be       |VERB |VBP |   3|We are the creatures you fear. |
|  1|   1|   3|the       |the      |DET  |DT  |   7|We are the creatures you fear. |
|  1|   1|   4|creatures |creature |NOUN |NNS |  11|We are the creatures you fear. |
|  1|   1|   5|you       |-PRON-   |PRON |PRP |  21|We are the creatures you fear. |
|  1|   1|   6|fear      |fear     |VERB |VBP |  25|We are the creatures you fear. |

Once here, I joined sentiment and tokens.


```r
tokens <- dplyr::mutate(tokens, word = enc2native(word))
tokens <- dplyr::mutate(tokens, word = tolower(word))
tokens <- dplyr::left_join(sentiment, tokens, by = c("saved_text" = "text", "word"))
head(tokens) %>% knitr::kable()
```



|saved_text                                                                                       |word      |sentiment | id| sid| tid|lemma    |upos |pos | cid|
|:------------------------------------------------------------------------------------------------|:---------|:---------|--:|---:|---:|:--------|:----|:---|---:|
|We are the creatures you fear.                                                                   |fear      |negative  |  1|   1|   6|fear     |VERB |VBP |  25|
|In darkness we walk.                                                                             |darkness  |negative  |  1|   1|   2|darkness |NOUN |NN  |   3|
|Standing tall, side by side, we shall build thy throne upon the burning ashes.                   |burning   |negative  |  1|   1|  15|burn     |VERB |VBG |  64|
|This is the dawn of the infernal reign.                                                          |infernal  |negative  |  1|   1|   7|infernal |ADJ  |JJ  |  24|
|A breath of the past so distant and so unreal. A soul condemned to haunt a frozen burial ground. |condemned |negative  |  1|   2|   3|condemn  |VERB |VBD |  54|
|A breath of the past so distant and so unreal. A soul condemned to haunt a frozen burial ground. |condemned |negative  |  1|   2|   3|condemn  |VERB |VBD |  54|

The `praise` package provides adjectives that I'll use to replace adjectives and to add an exclamation at the beginning of each text. Nouns, present and preterit verbs will be replaced with love/loves/loved because hey, this is pop music inspiration. I'll lose capital letters and won't bother too much, puppies probably do not care either. What I prepare below is what Hilary Parker called a dictionary table [in this tweet](https://twitter.com/hspter/status/948646331677118465). `VBZ` is for instance a verb in the present form like "haunts".


```r
modifiers <- tibble::tibble(pos = c( "NN",  "VBG", "JJ", 
                                     "VBD", "VB",  "NNS", 
                                     "VBP", "VBN", "NNP", "VBZ"),
                            modifier = c("love", "${adjective}", "${adjective}",
                                         "loved", "love", "lovers",
                                         "love", "${adjective}",
                                         "love", "loves"))
knitr::kable(modifiers)
```



|pos |modifier     |
|:---|:------------|
|NN  |love         |
|VBG |${adjective} |
|JJ  |${adjective} |
|VBD |loved        |
|VB  |love         |
|NNS |lovers       |
|VBP |love         |
|VBN |${adjective} |
|NNP |love         |
|VBZ |loves        |

Now let's get to work on the sweetening of the texts at last! Some occurrences of "hate" and "evil" remained, and I removed them by hand.


```r
# praise has some randomness, let's make it reproducible
set.seed(42)

modifiable_tweets <- dplyr::left_join(tokens, black_tweets, 
                                      by = c("saved_text" = "text"))

modifiable_tweets <- dplyr::left_join(modifiable_tweets, modifiers, 
                                      by = "pos")

# for easier use with map
replace_all <- function(pattern, replacement, x){
  stringr::str_replace_all(x, pattern = pattern,
                           replacement = replacement)
}

modified_tweets <- dplyr::group_by(modifiable_tweets, saved_text) %>%
  dplyr::summarize(praise_template = purrr::map2_chr(word, modifier, replace_all,
                                                  x = tolower(saved_text[1]))[1],
                   praise_template = paste("${exclamation}!", praise_template),
                   praise_template = stringr::str_replace_all(praise_template,
                                                              "hate", "love"),
                   praise_template = stringr::str_replace_all(praise_template,
                                                              "evil", "love"),
                   new_text = praise::praise(praise_template)) 

modified_tweets <- dplyr::select(modified_tweets, saved_text, new_text)

puppies_and_cats <- dplyr::left_join(modified_tweets, black_tweets, by = c("saved_text" = "text"))
head(puppies_and_cats) %>% knitr::kable()
```



|saved_text                                                                                       |new_text                                                                                            |created_at          |status_id          |
|:------------------------------------------------------------------------------------------------|:---------------------------------------------------------------------------------------------------|:-------------------|:------------------|
|A breath of the past so distant and so unreal. A soul condemned to haunt a frozen burial ground. |yikes! a breath of the past so distant and so unreal. a soul loved to haunt a frozen burial ground. |2017-12-28 20:35:00 |946479619020115968 |
|Be very afraid.                                                                                  |yippie! be very fine.                                                                               |2017-11-18 21:02:00 |931990900523307008 |
|Beware of the cat.                                                                               |yay! love of the cat.                                                                               |2017-12-11 14:54:02 |940233218204172288 |
|Beware the legions of Satan, they're ready for attack!                                           |ole! love the legions of satan, they're ready for attack!                                           |2017-12-05 22:48:00 |938178169399422976 |
|Born by the nothingness into eternal blasphemy.                                                  |mm! born by the nothingness into eternal love.                                                      |2017-12-03 04:15:01 |937173299448221696 |
|Born on a day of curses and damnation, I was doomed to hate.                                     |whoa! born on a day of lovers and damnation, i was doomed to love.                                  |2017-12-04 21:40:01 |937798672347226113 |

So although some new lyrics do not look that cheerful, they're at least grammatically correct.

# Replacing the picture

I recently discovered [Pexels](https://www.pexels.com/), a website with CC-0 pictures, that I even learnt to scrape for an unpublished (yet) project. So many photographs you can use and modify for free without any attribution! Quite cool, really. To scrape the page I first had to scroll down to get enough pictures, which I did following [this Stack Overflow thread](https://stackoverflow.com/questions/29861117/r-rvest-scraping-a-dynamic-ecommerce-page) with `RSelenium`. I tried using `seleniumPipes` instead but had trouble setting up the server and not too much time to dwell on that. 

Yes, you got it right, the code below automatically downloads pics of puppies into your laptop. Happy New Year.

```r
library("rvest")
library("RSelenium")
library("magrittr")
# https://stackoverflow.com/questions/29861117/r-rvest-scraping-a-dynamic-ecommerce-page
rD <- rsDriver()
remDr <- rD[["client"]]


# open the webpage
remDr$navigate("https://www.pexels.com/search/puppies/")

# scroll down
for(i in 1:30){      
  remDr$executeScript(paste("scroll(0,",i*10000,");"),
                      args = list("dummy"))
  # be nice and wait
  Sys.sleep(1)    
}

page_content <- remDr$getPageSource() 
remDr$close()

# functiosn for getting the pic links
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
links <- links[1:nrow(puppies_and_cats)]

# save
dir.create("data/puppies")
save_pic <- function(url, name){
  Sys.sleep(1)
  name <- paste0("puppy", name, ".png")
  
  try(magick::image_read(url) %>%
        magick::image_write(paste0("data/puppies/", name)),
      silent = TRUE)
}

purrr::walk2(links, 1:nrow(puppies_and_cats), save_pic)
```

I took a moment to browse the 66 pics. Carpe diem, carpe canes (I learnt Latin for 6 years as a teen(ager) and had to check the plural of _canem_...). 

The pics were a bit too big so I resized them.

```r
resize <- function(pic){
  magick::image_read(pic) %>%
    magick::image_resize("300x300") %>%
    magick::image_write(pic)
}
purrr::walk(dir("data/puppies/", full.names = TRUE), resize)
```

# Tweeting the cheerful tweets -- DON'T DO IT LIKE I DID!

I created [a Twitter account for the Bubblegum Puppies](https://twitter.com/cheerful_doggos) so that they could interact with the Black Metal Cats in their natural environment. I was asked by Scott whether I'd make a bot out of my idea and I don't intend to especially since my simplistic strategy can only answer tweets with negative words detected, but I'd be glad to let someone adopt the account, especially since I did not educate my puppies well to begin with as you see below! [Here](https://www.wjakethompson.com/post/tidyverse-tweets/) is a tutorial for making a Twitter bot with `rtweet`. In the meantime, I simply decided to tweet the answers I had created... without enough thinking.

_Note: It has been a long since I last obtained Twitter access tokens, so I refered to [the vignette](http://rtweet.info/articles/auth.html)._

Then I wrote the code to send replies, an important point being that you need to mention the username you answer to in your tweet otherwise the `in_reply_to_status_id` argument is ignored. It was a dangerous code because it sent too many replies, which made it a spam bot... Don't do that. So if you ever need an example of a very stupid spammer, think of me. Lesson learnt, I'll never be that reckless again, because I do not want to spam anyone.

```r
post_a_reply <- function(df, pic){
  rtweet::post_tweet(paste("@evilbmcats", df$new_text), in_reply_to_status_id = df$status_id,
                     media = pic)
  Sys.sleep(1)
}

pics <- dir("data/puppies/", full.names = TRUE)
purrr::walk2(split(puppies_and_cats, puppies_and_cats$status_id), pics,
            post_a_reply)
```

So how would I use the tweets if I could do it again? Well, I'd post them with a faaar bigger delay. And I think the account would be fun as a bot which'd reply only when the cats account tweets again. That way the puppies would be cute, not cute and annoying. Live and learn! Thanks to Bob Rudis to encourage me to post this! 

# Ending with some cuteness

And now, because I do not want you to think I'm now as depressed as a Black Metal Cat, I'll end this post by showing you a few replies thanks to the brand new `rtweet::tweet_shot` function added in the dev version of `rtweet` by [Bob Rudis](https://twitter.com/hrbrmstr?lang=en) after he saw my [#best9of2017 post](http://www.masalmon.eu/2017/12/30/best9of2017/). I resorted to saving the files and add the Markdown code to show them by hand but in a normal Rmd, not in a website, the images (`magick` objects) actually render very well. [Head to Twitter](https://twitter.com/cheerful_doggos/with_replies) to see the rest!



```r
save_one_tweet <- function(status_id){
  rtweet::tweet_shot(status_id) %>%
    magick::image_write(paste0("data/2018-01-04-",
                               status_id, ".png"))
}

c("948942337912328192",
  "948942293272334336",
  "948942240713531392") %>%
  purrr::walk(save_one_tweet)
```

<img src="/figure/2018-01-04-948942337912328192.png" alt="tweet">

<img src="/figure/2018-01-04-948942293272334336.png" alt="tweet">

<img src="/figure/2018-01-04-948942240713531392.png" alt="tweet">

