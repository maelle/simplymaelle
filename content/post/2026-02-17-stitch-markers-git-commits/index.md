---
title: "Git commits: please mark your stitches!"
date: '2026-02-15'
tags:
  - teaching
  - git
slug: stitch-markers-git-commits
---

In which I share a crochet analogy for Git commits…

*This post was featured on the [R Weekly highlights podcast](https://serve.podhome.fm/episodepage/r-weekly-highlights/222) hosted by Eric Nantz and Mike Thomas.*

## Don’t forget your stitch markers!

I am happily continuing my [crochet learning journey](/2026/01/26/amigurumi/). 
One lesson I have now internalised is that I should never ever forget or neglect to use stitch markers. 
Stitch markers are sort of small, not spiky, safety pins. 

{{< figure src="markers.jpg" width=400 alt="" caption="A crochet project featuring several stitch markers (beginning of a Snorlax from Lee Sartori's Pokemon crochet book)" >}}

Stitch markers should be used:

- at the beginning of a round to not forget where it started – in amigurumi you often crochet in a spiral so it’s hard to know where a round started,   
- every multiple of stitches to not have to count stitches above 12 even if you happen to have a PhD in counting[^phd]. Many crochet patterns will have you, say, “increase by two stitches every 8th stitch”, then “increase by two stitches every 9th stitch” so having your markers every 9 then 10 stitches will be most helpful. 

[^phd]: Also called PhD in statistics!

I have sometimes forgotten to place my stitch marker at the beginning of a round, and really regretted my lack of concentration. 
Furthermore, I used to think that adding more stitch markers was too much work (Opening the marker! Putting it under a stitch! Closing the marker! Could I be any lazier?!) which is absolutely wrong.
Stitch markers bring peace of mind. 
They spark joy.

## Don’t forget your Git commits!

Well, Git commits are just like stitch markers. 
Putting them regularly along your strand of work will only help you in the future. It is well worth the small annoyance of making them. 
With good commits, you can undo your work up to a certain point. You can understand what was done when and why.

In the [saperlipopette R package](/2024/01/18/saperlipopette-package-practice-git/) I’ve recently added exercises related to using history:  

- ["I want to find which commit deleted a file!"](https://docs.ropensci.org/saperlipopette/reference/exo_log_deleted_file.html) (`git log -- <path>`)    
- ["I want to find which commit deleted a line!"](https://docs.ropensci.org/saperlipopette/reference/exo_log_deleted_line.html) (`git log -S<string>`, `git log -G<regex>`)
- ["I want to understand ancestry references!"](https://docs.ropensci.org/saperlipopette/reference/exo_revparse.html) (`git show`, `git rev-parse`)
- ["I want to find who added a specific line!"](https://docs.ropensci.org/saperlipopette/reference/exo_blame.html) (`git blame`)
- ["I need to see what the project looked like at a certain version!"](https://docs.ropensci.org/saperlipopette/reference/exo_worktree.html) (`git worktree`)
- An older exercise: ["Hey I'd like to find which commit introduced a bug!"](https://docs.ropensci.org/saperlipopette/reference/exo_bisect.html) (`git bisect`)

Most of those exercises teach you the skill of obtaining a Git commit ID in answer to a question. 
How useful that is depends on how good the commit history is: big commits with uninformative messages are not great. 
If the exercises related to using the history seem relevant to you, try [improving your commit history](/2024/06/11/rewrite-git-history/).

{{< figure src="conventional.png" width=300 alt="Screenshot of commits on saperlipopette's GitHub repo where each type of commit gets a colored label, e.g. 'docs' or 'feature'" >}}

Note that you use [conventional commits](https://www.conventionalcommits.org/en/v1.0.0/), you might even get nice colours for them, just as if you were using stitch markers!
The above is due to RefinedGitHub, a browser extension for GitHub, but I'm sure similar functionality exist for other Git platforms and in IDEs.

## Conclusion  

In conclusion, I firmly believe good Git commits can change your life for the better, just like stitch markers. 
Don’t learn the lesson the hard way!

Besides, if you’re interested in learning how to recover from common Git mistakes such as a commit made on the wrong branch, consider registering to my upcoming workshop at Workshops for Ukraine:
["Oops, Git! How to recover from common mistakes"](https://sites.google.com/view/dariia-mykhailyshyna/main/r-workshops-for-ukraine#h.r3evto8phau7), 
Thursday, March 19th, 14:00 - 16:00 CET (Rome, Berlin, Paris timezone).
All proceeds going to support Ukraine. (After the workshop, [making a donation gets you access to the recording]( https://sites.google.com/view/dariia-mykhailyshyna/main/r-workshops-for-ukraine\#h.wqbtxn2ugxva))