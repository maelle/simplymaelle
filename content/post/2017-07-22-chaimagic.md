---
title: What's in our internal chaimagic package at work
date: '2017-07-22'
tags:
  - package-development
  - Shiny
  - shinydashboard
  - branding
slug: chaimagic
comments: yes
---


At my day job I'm a data manager and statistician for an epidemiology project called [CHAI](http://www.chaiproject.org/) lead by [Cathryn Tonne](https://twitter.com/cathryn_tonne). CHAI means "Cardio-vascular health effects of air pollution in Telangana, India" and you can find more about it in [our recently published protocol paper ](http://www.sciencedirect.com/science/article/pii/S1438463917301876). At [my institute](http://www.isglobal.org/) you could also find the [PASTA](http://www.pastaproject.eu/home/) and [TAPAS](http://www.tapas-program.org/) projects so apparently epidemiologists are good at naming things, or obsessed with food... But back to CHAI! This week Sean Lopp from RStudio wrote an [interesting blog post](https://rviews.rstudio.com/2017/07/19/supporting-corporate-r-user-groups/) about internal packages. I liked reading it and feeling good because we do have an internal R package for CHAI! In this blog post, I'll explain what's in there, in the hope of maybe providing inspiration for your own internal package!

<img src="/figure/source/2017-07-22-chaimagic/chaiteam_barcelona.jpg" alt="gestation" width="700">

As posted [in this tweet](https://twitter.com/chaiproject/status/832330667954315264), this pic represents the Barcelona contingent of CHAI, a really nice group to work with! We have other colleagues in India obviously, but also in the US.

<!--more-->

# The birth of `chaimagic`: help for data wrangling

_Note: part of this post was submitted as an abstract for the useR! conference in Brussels, for which I received feedback from my whole team, as well as from [François Michonneau](https://twitter.com/fmic_), [Dirk Schumacher](https://twitter.com/dirk_sch) and [Jennifer Thompson](https://twitter.com/jent103). Thanks a lot! I got to present a lightning talk about [`rtimicropem`](https://github.com/ropensci/rtimicropem) instead._

Right from the beginning of my time here in October 2015 I had quite a lot of data to clean, which I started doing in R of course. One task in particular was parsing filenames because those were informative in our project, containing for instance a date and a participant ID. I wrote a function for that, `batch_parse_filenames`, which parses all filenames in a vector and returns a list composed of two data.frames: one with the information contained in the filename (e.g. participant ID, date of sampling) and one with possible parsing mistakes (e.g. a nonexistent ID) and an informative message. It was a very useful function, and I packaged it up together with the data containing the participant IDs for instance. This was the beginning of an internal package! 

I decided to call it `chaimagic` because it sounded cool, and this despite knowing that there's absolutely no magic in the code I write.

# The growth of `chaimagic`: support for data analysis

`chaimagic` evolved with helper functions for analysis, e.g. a function returning the season in India from a date (e.g. monsoon, winter), or functions for common operations on variables from our questionnaire data. `chaimagic` also got [one more contributor](https://twitter.com/MilaCarles).

`chaimagic` also contains a `Shiny` dashboard for exploring personal monitoring data that were collected in the project: one selects a participant-day and gets interactive `leaflet` and `rbokeh` plots of GPS, air quality, and time-activity data from questionnaires and wearable cameras. I told my boss that the dashboard was brought to us by the [biblical Magi](https://en.wikipedia.org/wiki/Biblical_Magi) but maybe I helped them, who knows. 

# The maturity of `chaimagic`: serving scientific communication

Now, our package also supports scientific communication thanks to an `RMarkdown` template for internal reports which fostered reproducibility of analyses by making the use of `RMarkdown` even more appealing. Having an `RMarkdown` template also supports consistent branding, which is discussed in the RStudio blog post.

`chaimagic` also includes a function returning affiliation information for any possible collaborator we have; and a function returning the agreed-upon acknowledgement sentences to be put in each manuscript. These are pieces of information we can be very happy not to have to go look for in a folder somewhere, we can get them right without leaving R!

# Why is `chaimagic` a really cool package?

`chaimagic` has been a valuable tool for the work of two statisticians/data managers, one GIS technician, one post-doc and one PhD student. Discussing its development and using it was an occasion to share our R joy, thus fostering best practices in scientific computing in our project. Why use Stata or other statistical softwares when you have such a nice internal R package? We found that even if our package served a small team of users, an R package provided a robust infrastructure to ensure that everyone was using the same code and R coding best practices in our team.

![](/figure/source/2017-07-22-chaimagic/chaimagic.jpg)

So this is very good... but we all know from e.g. [Airbnb](https://twitter.com/i/web/status/855422698230599680) (see also [this post](https://medium.com/airbnb-engineering/using-r-packages-and-education-to-scale-data-science-at-airbnb-906faa58e12d) about Airbnb and R) that *stickers* are a very important aspect of an internal R package. I was over the moon when [my colleague Carles](https://twitter.com/MilaCarles) handed me these fantastic `chaimagic` stickers! He used [the `hexSticker` package](https://github.com/GuangchuangYu/hexSticker). I'll leave the team in September after everyone's vacations, and this piece of art was the goodbye present I received at my goodbye lunch. Doesn't it make our internal package completely cool now?
