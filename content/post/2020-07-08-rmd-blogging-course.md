---
title: "How I Taught Scientific Blogging with R Markdown, Online"
date: '2020-07-08'
tags:
  - css
  - hugo
slug: rmd-blogging-course
---

Last week I had the pleasure to lead an online course about "Scientific Blogging with R Markdown", invited by [Najko Jahn](https://twitter.com/najkoja) and [Anne Hobert](https://twitter.com/ahobert) from [SUB Göttingen](https://www.sub.uni-goettingen.de/en/news/).
To follow the [example set](https://alison.rbind.io/post/2020-06-02-tidymodels-virtually/) by the [incredible Alison Hill](https://alison.rbind.io/), I'll write a summary of what I've learnt and would like to do better next time.

## The topic

The topic of the course was "Scientific Blogging with R Markdown".
For months I would sometimes write down some ideas, from "present distill" to "show web developer console", that I had whilst reading things online.
I was already interested in topics related to R Markdown blogging and website development, but having the course on my agenda made me pay even more attention.
I did my note taking in a physical notebook and then I set up a GitHub repo to use its issue tracker.

A bit later I had to start drafting an actual schedule, and decided I would try and not recommend a single tool, but instead show three ways of blogging with R Markdown: using the distill package whose goal is scientific and technical writing; using Hugo since that's what I use at work; using WordPress since this comes up on Twitter every so often and seems like a good alternative workflow for when one does not use Git yet.
I thought it might help each attendee see a tool that suit them better, and that it might mean everyone would learn something.
I suppose it also helped reduce my responsability if folks ended up not liking their tool, since I did not recommend a single one.  :stuck_out_tongue_winking_eye:

The course summary I wrote was

_Are you an R user who works in science? Would you like sharing more online? How about starting a new blog, with R Markdown (Rmd)?_

_In this 2-hour course with live coding, we’ll go on three short adventures:_

* _setting up a scientific Rmd blog with the Distill framework and the distill package_

* _setting up a scientific Rmd blog with the Hugo website generator and the hugodown package_

* _adding Rmd posts to a Wordpress blog._

_We’ll prepare for the adventures by defining what we expect of an Rmd blog. We’ll end the course by reflecting on each adventure as well as mentioning important future paths such as how to promote your blog. You should leave the course ready to start a scientific R Markdown blog with your tool of choice, and knowing where to find more resources and help._

## The practical plan

When I first said yes to giving the course a while ago, it was supposed to happen in person in Göttingen in Germany, but unsurprisingly that plan changed a few months ago.
Online it would be! With Zoom.
Anne Hobert and Najko Jahn, who organized the workshop and taught basic R Markdown on the first day, were technical helpers on the second day.

My course was to take place on the second day, in two hours.
In the afternoon attendees would have time to work together in breakout rooms or on their own, and I stayed online for questions, as did Anne and Najko.

Anne had the idea to open my course to more attendees than the workshop first day i.e. open it to 10 attendees outside of the funding stream, bringing the maximum to 20 attendees.
We decided to advertise it via R-Ladies communication channels (telling R-Ladies could share the info with their friends and colleagues from under-represented groups), and Anne gave spots on a first come first serve basis.

There was an online pad associated with the course, where attendees could write who they were (so cool to see that) and their questions.
We had a short call at the beginning of the week to ensure we could hear each other.
The three of us read [The Carpentries guidance for teaching online](https://carpentries.org/blog/2020/03/tips-for-teaching-online/) and e.g. used the idea to ask attendees to write "hand" and "hand helper" in the Zoom chat if they wanted to ask a question to the instructor (me) or a helper.

## A course website

Since it was a course about blogging, setting up a website seemed natural.
It was also an excellent way to productively procrastinate if I'm being honest, but in the end I really liked filling and working with [the course website](https://scientific-rmd-blogging.netlify.app/) so I'd say it was time well spent?

I used two Hugo themes, 

* [Hugo theme learn](https://github.com/matcornic/hugo-theme-learn) (that has a nice table of contents on the left, search, copy-paste button for code...)
* [Hugo theme reveal-hugo](https://github.com/dzello/reveal-hugo) (for slides)

Slides for each section are listed in the menu and opened in a new tab (thanks to a [custom menu layout](https://github.com/maelle/rmd-blogging-course/blob/main/layouts/partials/menu.html), compared to the original Hugo learn theme).

Some Markdown content was generated with [R Markdown](https://rmarkdown.rstudio.com/), using [hugodown](https://github.com/r-lib/hugodown/).

The website is deployed by [Netlify](https://www.netlify.com/).

Slides could be printed to PDF using Decktape which I [had done in a concept](https://github.com/maelle/test-course-site) but I did not pursue it further.

Why use Hugo for both the website and slidedecks, and not, say Hugo+hugodown for pages and xaringan for slides?
This way the source of slides is html produced by Hugo from Markdown content.
It allowed me to use:

* downlit syntax highlighting for slides created from R Markdown with hugodown output format;
* Chroma syntax highlighting for other languages;
* emojis! 
* Shortcodes in slides, which I ended up doing for diagrams.

Also, because slides are in the content, they are indexed by the Hugo learn theme so searchable!

I learnt a few Hugo tricks whilst setting up the website which was fun per se, and I really liked the end product, as mentioned above. Two highlights for me were 

* to have a table of contents on the left that I could use on the day of the course
* the page with snippets for demos that solved my problem "How am I going to organize the small pieces of code I need for demos?". 

The website also had questions for topics not mentioned during the course, where I e.g. stuffed the content from the notes I had taken.

## The newest tools :grimacing:

In two parts of the course I actually demo-ed packages in development which is not optimal, but I had excuses each time.

* For using R Markdown and Hugo, I never fully used blogdown myself. When one day in my GitHub timeline I saw Hadley Wickham create [hugodown](https://hugodown.r-lib.org/), I got very excited because it fit more with the way I had used Hugo and R Markdown. I started using it so that was the tool I wanted to share about. I think one can trust hugodown's maintainer. :grin:

* For using R Markdown and WordPress, the existing function relies on unmaintained packages (XML and RCurl), whereas there is a REST API associated with WordPress websites nowadays. I wrote a minimal package called [goodpress](https://maelle.github.io/goodpress/) with which it is possible to write directly from R Markdown to WordPress. It does not work for all WordPress packages yet, but I think that with Bob Rudis' efforts in [pressur](https://github.com/hrbrmstr/pressur), with some work (and contributors :wink:) things could work for all WordPress websites. I would only recommend goodpress on _existing_ websites at the moment, where it can only be a better idea than trying to use RCurl IMHO.

I hope my different warning signs will have helped the learners see what's stable vs what's not.

## What we did

During the course two hours, I shared my whole desktop with the attendees[^screen].
I had several slidedecks integrated in my website.

The slidedecks related to the three tools had a slide telling it was time for a demo, and then a few slides ending with a break countdown (I had to remove the last break).
For the demos, I had printed my notes after knitting them to PDF.
The demos were my doing website things in RStudio and Firefox.
Compared to the demos on the website, for hugodown I only showed `create_site_academic()`, not how to make another theme hugodown-compatible.

In the end I took time to present my decks about reproducibility and about interactions with readers (including blog promotion and analytics).

It was a bit intense, which I need to think about if I teach this again.

In the few hours after my course, some attendees remained in breakout rooms (I got one question I think, about a Pandoc installation issue) and two of them showed their experiments in the final sharing session (where there were more than the two still online, although some people understandably had to leave).

## Questions I got

And my tentative answers.

### (about distill) Is there a difference between knitting and using the build tab?

_I think at that point I realized I'd need to use distill more regularly to differentiate the different parts and the magic happening from RStudio._

Apparently from RStudio the website gets built when one edits the site configuration, and when one knits a post. You need to knit posts; and if you edit about.Rmd you need to either knit it or re-build the website. My impression is that you need to knit with intent, but most often the build part happens magically.

### What is the difference (pros/cons) between hugo and distill?

I mentioned it a bit, distill is perfect for scientific/technical blogging but not very flexible. Hugo is very flexible which might be a curse. :wink: With Hugo you can really build any website: for this course website I mixed two Hugo themes, with a few custom layouts for instance (so even the slides used hugodown). But it requires time for learning. You could try both before committing, and you could still switch tools one day even if that'd take time.

### Is it possible to have one citation style file for all posts/folders with Hugo or does it need to be in the same folder as the post?

* For distill not yet https://github.com/rstudio/distill/issues/82 

* For hugodown not yet either https://github.com/r-lib/hugodown/issues/28 

So for both distill and hugodown, it's not possible *yet* but at least you can store .bib at the root and refer to it as ../../../refs.bib or so in your post YAML.

### How to limit the readership of your blog - if i want my website to only be viewable to the people in my company?

For WordPress websites having private posts seem to be built-in. 

For Hugo and distill it depends on how you deploy. I.e. from https://hugodown.r-lib.org/articles/deploy.html if you choose say Amazon S3 you could look for "Amazon S3 private website". 
Such a feature might not be in the free tier of the service.

Or you could deploy to a server that your team has? 

### Regarding reproducibility: Do you have any thoughts on how to share your data belonging to the code in such a blogpost?

You could distribute it with your Hugo website (in the post folder or from static -- I haven't tried), otherwise sharing it in a GitHub repo works; but better for bigger datasets would be to use Figshare/Zenodo/etc.

### Using utterance.es, you have some sort of notification if someone comments on your post?

Yes, same as other GitHub notifications (so email or web depending on your settings).

### Is there a way to collaborate on the same blog?

Yes definitely. At rOpenSci we use GitHub for that (we even have a guide, [blogguide.ropensci.org](https://blogguide.ropensci.org/)), you could use GitLab too. Adding posts by pull requests is nice because of the pull requests review infrastructure. Without a git platform I think it might be less natural to edit collaboratively but you could still find an alternative workflow.

### Is it a good idea to use the distill format for the lab where you work instead of being a personal blog?

I've seen [distill for groups](https://scientific-rmd-blogging.netlify.app/distill/further-resources/#websites-for-inspiration-), I think it works well for that too, if your colleagues are ok editing from R / letting you edit things.

### Do you know cheap hosting services?

That question was a good reminder of my privilege: for me a custom domain costing a few dollars a month is not expensive.
For others, especially in some countries, it can be a luxury.
In that case, using GitHub pages, free Netlify domains, etc. is a good workaround and better than having no website.

### Comment by Anne: GDPR, privacy page, in Germany imprint.

I shortly mentioned one should add a privacy page mentioning what data is collected, and Anne Hobert reminded attendees that in Germany all websites need to have a page called Impressum/imprint.

## Possible improvements

I might get even more ideas from participants[^participants] but here are a few ones:

* I think next time it'd make sense to set up a second device to see the Zoom screen at all times. Or a second screen if Zoom lets me share one screen only.

* I would not be that ambitious regarding what one can show with hugodown.

* I'd like to know more about distill and WordPress to feel more at ease showing them, or to let someone else teach these parts.

* I am not sure _how_, but I'd like to interact more with attendees.

A big limitation of the format, and the way I taught (giving a few recipes but not ensuring attendees live with a website), is that I can't know attendees will actually go and build a website.
Or maybe I'll know, in a few weeks, if/when I receive URLs.
I think that with websites, there's a need for technical knowledge for sure, but maybe also some sort of accountability/support group.

## Thank you

I'd like to thank Najko Jahn and Anne Hobert for trusting me to give this course and for being such helpful and nice collaborators.
My course website has a [credits page](https://scientific-rmd-blogging.netlify.app/credits) where I'm sure I forgot names. :grimacing:

## Next time?

I'd love to teach the topic again, re-using and building upon my materials.
If you're the organizer of a meetup for under-represented R users, get in touch to see if my skills and availabilities can be a fit for your group.
If you're organizing something else, let's talk business i.e. in that case I wouldn't teach for free. :wink:
In all cases, I'm looking forward to keep learning new things myself.

If you taught or attended a workshop about blogging and R Markdown, feel free to share your own insights and good ideas. :pray:

[^screen]: Sharing my whole screen makes me quite nervous. I had written a list of things to do before hand such as making sure Slack and Mattermost were closed and wouldn't send any notification.
[^participants]: Participants were sent a pin.up link to add feedback on sticky notes [à la Carpentries](https://twitter.com/ma_salmon/status/974606612773163008).