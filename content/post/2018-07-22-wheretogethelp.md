---
title: Where to get help with your R question?
date: '2018-07-22'
slug: wheretogethelp
comments: yes
---

Last time I blogged, I offered [my <s>obnoxious</s> helpful advice for blog content and promotion](/2018/07/16/soapbox/). Today, let me again be the agony aunt you didn't even write to! Imagine you have an _R question_, i.e. a question related to how you can do something with R, and your search engine efforts haven't been too successful: where should you ask it to increase your chance of its getting answered? You could see this post as my future answer to stray suboptimal Twitter R questions, or as a more general version of Scott Chamberlain's excellent talk about how to get help related to rOpenSci software in the [2017-03-07 rOpenSci comm call](http://communitycalls.ropensci.org/).

I think that the general journey to getting answers to your R questions is first trying your best to get answers locally in the documentation of R, then to use a search engine, and then to post a well-formulated question _somewhere_. My post is aimed at helping you find that _somewhere_. Note that [it's considered best practice to ask in one _somewhere_ at once](https://community.rstudio.com/t/faq-is-it-ok-if-i-cross-post/5218), and to then move on to another _somewhere_ if you haven't got any answer... or if someone kindly redirects you to a better venue!

<!--more-->

One thing this post isn't about is _how_ to ask for help to _humans_, which is a topic that's e.g. covered very well in [Jenny Bryan's talk about `reprex`](https://vimeo.com/208749032) in the [same 2017-03-07 rOpenSci comm call](http://communitycalls.ropensci.org/), but I'll link out to useful resources I've found. This post is also not about _how_ to ask for help to e.g. Google, and I don't know of a good search engine guide yet although e.g. "It can be particularly helpful to paste an error message into a search engine to find out whether others have solved a problem that you encountered." in https://www.r-project.org/help.html is true.

# Public question platforms vs. safe spaces?

I'll start this post with a general comment for newbies who aren't at ease enough yet to post their question, no matter what type of questions (see later), to anywhere public: find your safe and friendly space! 

## Lean on your current friend(s)

Do you have an R friend? Maybe this colleague who actually convinced you to start using R? Well, ask this person for help! Remind them it's their fault you're struggling now. 

And if you're not a newbie but are reading this now, be this R friend! Mentor newbies on their way to be better R users and askers.

## Find new friends

Join a community, and ask your questions there. More specifically, you can join the [R4DS community](https://www.jessemaegan.com/post/r4ds-the-next-iteration/  ); your [local](https://rladies.org/)/[remote](http://eepurl.com/dy1bm1) R-Ladies community (as a reminder, [this doesn't mean women only](https://rladies.org/about-us/) but instead "minority genders (including but not limited to cis/trans women, trans men, non-binary, genderqueer, agender)"); this [French-speaking community]( https://lstu.fr/rgrrr). Such communities most often have Slack workspaces or equivalent, with an r-help channel, as well as a code of conduct and a general friendly attitude. Build your confidence there, and soon, you'll be out asking (and answering!) R questions in the wilderness as well.

Note, if you know of any list of such friendly communities, please tell me. I only know of [this list (of lists) of R User Groups and Conferences by David Smith](http://blog.revolutionanalytics.com/local-r-groups.html).

# Spotlight on my typology of R questions

One thing I especially like about Scott Chamberlain's slidedeck is his typology of methods to get help, ["methods and what they're good for"](https://scotttalks.info/qropensci/#/good-for). I'll build this post based on a typology of questions, that also intersects with a typology of your newbiness-nervousness.

I tend to think of R questions as pertaining to one of these categories:

* is this a bug, why am I stuck with this code? how do I install `rJava` on my laptop? (problem!) Problems encompass actual bugs, and problems where the problem is your code rather than the package (I'll admit that I find joy in my code's sometimes not being the problem, although this doesn't solve the problem.).

* is there a tool in R for doing `foo`? how do I learn about `bar`? (question!)

* what's the best plotting library in R? dependencies, is less more? (debate!)

I agree that the difference between _debate_ and _question_ might be thin, in my mind _question_ questions have more of a trivia aspect, with answers being easier.

# Where to ask your _problem_ question

Scenario: you wrote R code, and it doesn't do what you expected it to, which includes the glorious cases of not running at all or of using packages you haven't even been able to install. What do you do then?

* If your problem is linked to a package in particular, i.e. you know the function that's causing you sorrow comes from the `foo` package, then your question is a `foo` question or bug report. So what you need to find now is the platform to report bugs and ask questions about `foo`. 

    * It might be written in `foo` docs/metadata. Have a look in the docs, and observe the website or development repository of the package. E.g. if you use `blogdown`, its BugReports URL is https://github.com/rstudio/blogdown/issues and if you were to try and open an issue there with your bug or other problem, you'd get to see [the template that tells you to use Stack Overflow first for general questions](https://github.com/rstudio/blogdown/blob/master/.github/ISSUE_TEMPLATE.md#consider-stackoverflow-first). GitHub repos can also contain question guidance as part of their CONTRIBUTING.md. If you use `INLA` and have questions, you'll find http://www.r-inla.org/help useful. I remember having many questions about `INLA` during my PhD, and sometimes having to ask them in the mailing list when the docs couldn't help me any further.
    
    * You'll rarely have to email the maintainer of a package, but you might have to, e.g. if you wonder whether the package will be further maintained or [as Scott said here, if your problem is specific to some sensitive data and an rOpenSci package](https://scotttalks.info/qropensci/#/good-for).

    * Sometimes `foo` provider can also be a clue. If `foo` is an [rOpenSci package](https://ropensci.org/packages/), you can ask for help on [rOpenSci's forum](https://discuss.ropensci.org/) or on Stack Overflow using the "ropensci" tag. If `foo` is an RStudio package, you can ask for help on [RStudio's forum](https://community.rstudio.com/)... You get the gist. 

* When there's no other indication or you're not sure `foo` or `bar` is the actual culprit, use a more general site like [Stack Overflow](https://stackoverflow.com/), tagging your question with "r" and other relevant labels. I was pleased to see that the bottom of https://www.r-project.org/help.html includes a list of question venues including Stack Overflow, but also [all R mailing lists](https://www.r-project.org/mail.html).

# Where to ask your _question_ or _debate_ question

How to find out whether there's a tool to do image manipulation in R? How to know what's best practice for your special package development challenge? How to get a book recommendation?

Maybe Twitter, to which I'll dedicate a whole section a bit later. But a good guess is also trying to locate where the experts, or people interested in the answer, normally interact. Your finding your happy place will probably be a bit of a trial-and-error process, so while asking your first question might be more difficult, things should be easier as you learn to navigate the different communities and their activity. Here is some inspiration:

* [The R SIG (special interest group) mailing lists](https://www.r-project.org/mail.html). Now _I_ don't use them at all because I'm not keen on receiving and sending many emails, but I can see there's a great diversity of groups, maybe one that'll suit your interests!

* If your question is related to open science, reproducibility, documenting data, extracting data out of something (a PDF! a PNG!), package development, [rOpenSci's discuss forum](https://discuss.ropensci.org/) might be the right venue. 

* The [RStudio community forum](https://community.rstudio.com/) has many categories including the `tidyverse` and the RStudio IDE of course but also about package development; R in production, at scale, and in complex environments, etc.

* For a question related to outbreak analytics and R programming, check out [the R Epidemics Consortium mailing list or Slack](http://www.repidemicsconsortium.org/forum/).

# What is Twitter good for?

Anyone can use Twitter for what they want, depending on the accounts they follow and on their way to consume their timelines and hashtags, so the following is probably even more personal to me than the rest. I think Twitter questions that have higher chances to be answered (because I like answering them!) are the trivia ones, i.e. "is there something to do `foo`", and the more controversial ones if only for having fun. That said, if you don't have a huge following yet, or post at the "wrong" time, even when using the right hashtags or so your question might be ignored, keep that in mind.

What Twitter is not good for is, in my opinion:

* Asking a question about a package by tagging the maintainer especially when the package docs state there's another venue for that. But then, as a maintainer, one should probably handle such mistakes gracefully (like Yihui did [here](https://twitter.com/xieyihui/status/1015079496427491328)).

* Too much code I think, even if tools like [Sean Kross' `codefinch`](https://github.com/ropenscilabs/codefinch) can help. Your tweet with a question can circumvent the difficulty by advertising a gist like codefinch makes you do (i.e. a tweet with an "attachment"), or by being a link to your actual question on Stack Overflow (i.e. a tweet to try and draw attention to your question).

* Building an archive of questions and answers. It won't be easy at all to search whether a question has already been answered. You can counteract this loss of knowledge by asking questions somewhere else, or building something out of the answers you've been given, e.g. a [blog post listing resources for geospatial analysis in R](https://itsalocke.com/blog/r-spatial-resources/) or [resources about transport and GIS in R](https://www.stephdesilva.com/post/the-keys-to-the-kingdom/) like Steph Locke and Steph de Silva, respectively, did after getting answers on Twitter.

* Surprise questions. At least I am scared of questions phrased like this: "Who has time to help me with <insert very general term here>?" (e.g. XML, web scraping). They sound like a trap to me! I'd rather know more precisely what the person wants.

# Conclusion

I think it's as important to ask your questions in the right place as it is to ask them in the right way. I hope this post provided some clues as to know where to ask R questions. I'd like to make an important remark from [Mara Averick](https://maraaverick.rbind.io/) here: "You should not take being redirected to a different forum as a criticism! It's 100% a means of helping you get the help you need!". Truth!

I'll round up this post with a few links of resources to learn _how_ to ask your question:

* The guidelines and code of conduct of the venues where you've chosen to post your question, if there are some!

* the [2017-03-07 rOpenSci comm call](http://communitycalls.ropensci.org/) "How to Ask Questions so they get Answered! Possibly by Yourself!" was so, so good. I've already mentioned the talk by Jenny Bryan about `reprex` and Scott Chamberlain's talk about getting help about rOpenSci's software. Furthermore, JD Long reviewed the historical challenges of getting R help.

* I've seen very useful posts on RStudio Community forum when browsing the FAQ tag. In particular, I like ["Tips for Introducing Non-Programming-Problem Discussions"](https://community.rstudio.com/t/faq-tips-for-introducing-non-programming-problem-discussions/6871) and [this more general list of FAQs](https://community.rstudio.com/t/frequently-asked-questions/35).

Good luck with your questions! The Disqus thingy below is good for suggestions and questions about this post!
