---
title: "How I Taught Scientific Blogging with R Markdown, Online"
date: '2020-07-08'
tags:
  - css
  - hugo
slug: css-snippet
---

Last week I had the pleasure to lead an online workshop about "Scientific Blogging with R Markdown", invited by [Najko Jahn](https://twitter.com/najkoja) and [Anne Hobert](https://twitter.com/ahobert) from [SUB Göttingen](https://www.sub.uni-goettingen.de/en/news/).
To follow the [example set](https://alison.rbind.io/post/2020-06-02-tidymodels-virtually/) by the [incredible Alison Hill](https://alison.rbind.io/), I'll write a summary of what I've learnt and would like to do better next time.

## The topic

The topic of the course was "Scientific Blogging with R Markdown".
For months I would sometimes write down some ideas, from "present distill" to "show web developer console", that I had whilst reading things online.
A bit later I had to start drafting an actual schedule, and decided I would try and not recommend a single tool, but instead show three ways of blogging with R Markdown: using the distill package whose goal is scientific and technical writing; using Hugo since that's what I use at work; using WordPress since this comes up on Twitter every so often and seems like a good alternative workflow for when one does not use Git yet.
I thought it might help each attendee see a tool that suit them better, and that it might mean everyone would earn something.

## The practical plan

When I first said yes to giving the course a while ago, it was supposed to happen in person, but unsurprisingly that plan changed a few months ago.
Online it would be! With Zoom.
Anne Hobert and Najko Jahn, who organized the workshop and taught basic R Markdown on the first day, were technical helpers on the second day.
There was an online pad associated with the course, where attendees could write who they were (so cool to see that) and their questions.
We had a short call at the beginning of the week to ensure we could hear each other.
The three of us read [The Carpentries guidance for teaching online](https://carpentries.org/blog/2020/03/tips-for-teaching-online/) and e.g. used the idea to ask attendees to write "hand" and "hand helper" in the Zoom chat if they wanted to ask a question to the instructor (me) or a helper.

## A course website

Since it was a course about blogging, setting up a website seemed natural.
It was also an excellent way to productively procrastinate if I'm being honest, but in the end I really liked filling and working with [the course website](https://scientific-rmd-blogging.netlify.app/)!

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
* emojis! `:grin:` works in slides;
* Shortcodes in slides, which I ended up doing for diagrams.

Also, because slides are in the content, they are indexed by the Hugo learn theme so searchable!

I learnt a few Hugo tricks whilst setting up the website which was fun per se, and I really liked the end product, as mentioned above. Two highlights for me were 

* to have a table of contents on the left that I could use on the day of the course
* the page with snippets for demos that solved my problem "How am I going to organize the small pieces of code I need for demos?". 

## Questions I got

Q (distill): Is there a difference between knitting and using the build tab?
Maëlle: apparently from RStudio the website gets built when one edits the site configuration, and when one knits a post. You need to knit posts; and if you edit about.Rmd you need to either knit it or re-build the website. My impression is that you need to knit with intent, but most often the build part happens magically.

Q: what is the difference (pros/cons) between hugo and distill?
Maëlle: I mentioned it a bit, distill is perfect for scientific/technical blogging but not very flexible. Hugo is very flexible which might be a curse. ;-) With Hugo you can really build any website: for this course website I mixed two Hugo themes, with a few custom layouts for instance (so even the slides used hugodown). But it requires time for learning.

Q: Is it possible to have one citation style file for all posts/folders with Hugo or does it need to be in the same folder as the post?
Maëlle: I'm exploring now. For distill apparently no https://github.com/rstudio/distill/issues/82 For hugodown EDIT I had actually looked into it! https://github.com/r-lib/hugodown/issues/28 So for both distill and hugodown, it's not possible *yet* but at least you can store .bib at the root and refer to it as ../../../refs.bib or so in your post YAML.

Q: How to limit the readership of your blog - if i want my website to only be viewable to the people in my company?
Maëlle: For WordPress websites having private posts seem to be built-in. For Hugo and distill it depends on how you deploy. I.e. from https://hugodown.r-lib.org/articles/deploy.html if you choose say Amazon S3 you could look for "Amazon S3 private website". Or you could deploy to a server that your team has?

Q: Regarding reproducibility: Do you have any thoughts on how to share your data belonging to the code in such a blogpost?
Maëlle: you could put it with your post in Hugo (in the post folder), otherwise sharing it in a GitHub repo works; but better for bigger datasets would be to use Figshare/Zenodo/etc.

Q: Using utterance.es, you have some sort of notification if someone comments on your post?
Maëlle: yes, same as other GitHub notifications (so email or web depending on your settings).

Q: Is there a way to collaborate on the same blog?
Maëlle: Yes definitely. At rOpenSci we use GitHub for that (we even have a guide, blogguide.ropensci.org), you could use GitLab too. Adding posts by pull requests is nice because of the pull requests review infrastructure. Without a git platform I think it might be less natural to edit collaboratively but you could still find an alternative workflow.

Q: Is it a good idea to use the distill format for the lab where you work instead of being a personal blog?
Maëlle: I've seen distill for groups (https://scientific-rmd-blogging.netlify.app/distill/further-resources/#websites-for-inspiration-), I think it works well for that too, if your colleagues are ok editing from R / letting you edit things.

Q: cheap hosting?

Comment: GDPR, privacy page, in Germany imprint.

## Possible improvements

I am still waiting to receive feedback from part of the attendees if they wish to, but I already have a few ideas myself

* I think next time it'd make sense to set up a second device to see the Zoom screen at all times.

* I would not be that ambitious regarding what one can show with hugodown.

* I'd like to know more about distill and WordPress to feel more at ease showing them, or to let someone else teach these parts.

* I am not sure _how_, but I'd like to interact more with attendees.

A big limitation of the format, and the way I taught (giving a few recipes but not ensuring attendees live with a website), is that I can't know attendees will actually go and build a website.
Or maybe I'll know, in a few weeks, if/when I receive URLs.


## Next time?

I'd love to teach the topic again, re-using and building upon my materials.
If you're the organizer of a meetup for under-represented R users, get in touch to see if my skills and availabilities can be a fit for your group.
If you're something else, let's talk business i.e. in that case I wouldn't teach for free. 