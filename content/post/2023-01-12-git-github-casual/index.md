---
title: "git and GitHub in R for the casual user"
date: '2023-01-12'
tags:
  - git
  - GitHub
slug: git-github-casual
output: hugodown::hugo_document
rmd_hash: b32c0e87e795fff3

---

If you've been taught git and GitHub but practice so rarely that you're discouraged, what should you do to re-start more easily? Let's imagine you have to, or really want to, use git and GitHub for your next analysis project. Here's what I would recommend...

*I assume you already own a GitHub account. If not, refer to [happygitwithr guidance](https://happygitwithr.com/github-acct.html).*

*Thanks to the people who shared recommendations on Mastodon, whose names are acknowledged in the rest of the post!*

## Prepare your tissues...

Or rather your support system. Make sure a collaborator with more git and GitHub practice is on call! If not, remember [where you post your R questions](/2018/07/22/wheretogethelp/). This way if you get stuck somewhere along the line, things will get less desperate.

You might also want to prepare your favorite hot beverage, but you probably won't be able to read git guidance from tea leaves, hence the need for an actual support plan.

## Clear notifications

This step is not completely necessary but could help you for the next re-start. Look at your [GitHub notifications](https://github.com/notifications)! Hopefully you weren't getting them by email the whole time...

If you see you've been getting far too many notifications, tweak the settings!

-   [Notification settings](https://github.com/settings/notifications);
-   [Threads (issues, PRs) you are subscribed to](https://github.com/notifications/subscriptions);
-   [Repositories you are watching](https://github.com/watching) (I find [custom watching](https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github/setting-up-notifications/configuring-notifications#configuring-your-watch-settings-for-an-individual-repository) much more rewarding).

This way next time you open GitHub, your notifications should have a better signal to noise ratio.

Now if you notice you've been missing important notifications, try to either commit to log in more often or get those notifications out of GitHub and into your preferred system. For instance, tell collaborators it's safer to email you. Another example: maybe don't watch dplyr repository, but do subscribe to the Tidyverse blog.

## Inject a bit of motivation

Why are you doing this anyway? :sweat_smile: Good reads are the ["Big picture" section of happygitwithr](https://happygitwithr.com/big-picture.html) (by Jenny Bryan and collaborators) and the [Openscapes GitHub illustrated series](https://www.openscapes.org/blog/2022/05/27/github-illustrated-series/) by Allison Horst and Julie Lowndes.

## Decide whether to use GitHub Desktop

After I first shared my post on Mastodon, several people ([Robert M Flight](https://mastodon.social/@rmflight/109676738327559708), [Steven P Sanderson](https://mastodon.social/@stevensanderson@mstdn.social/109677107513778953), [Jim Gardner](https://mastodon.social/@jimgar@fosstodon.org/109678458557655706), [Francisco Rodriguez-Sanchez](https://mastodon.social/@frod_san@ecoevo.social/109677386173703866)) recommended using [GitHub Desktop](https://docs.github.com/en/desktop). Its nice interface would, as far as I understand, replace usethis. It'd mean you have R open somewhere, do your thing there, and regularly you do the git/GitHub stuff in the GitHub Desktop interface (setting up a GitHub repository, commits, pushes).

## Check your R/GitHub situation

Skip if you use GitHub Desktop (and have installed git!). :wink:

In this I assume you have R and RStudio IDE installed. One problem at a time. :smile_cat:

[Tip by Hao Ye](https://mastodon.social/@hye@glammr.us/109676766952787983): first [setup R so that it loads usethis at each session start](https://usethis.r-lib.org/articles/usethis-setup.html), so you might skip writing `usethis::` in front of each usethis function.

Let's see if you have git correctly installed and hooked with GitHub, and remediate if not. Run [`usethis::git_sitrep()`](https://usethis.r-lib.org/reference/git_sitrep.html). Also run [`usethis::gh_token_help()`](https://usethis.r-lib.org/reference/github-token.html).

You might also want to run the whole [usethis article about git and gitHub credentials](https://usethis.r-lib.org/articles/git-credentials.html). On Linux, you might need [Managing GitHub credentials from R, difficulty level linux](https://blog.djnavarro.net/posts/2021-08-08_git-credential-helpers/) by Danielle Navarro.

For git installation, refer to [happygitwithr guidance](https://happygitwithr.com/install-git.html).

Do not be surprised if your tokens expired since the last time you used GitHub from R. Also do not be surprised if it all takes a little while. Take notes on what you're doing. Next time you might be able to re-start after just running the two sitrep functions!

When re-creating tokens, make sure to give them an informative name (related to the computer you use them for, the project you use them for) as this is very useful for token management. I swear that no longer calling my tokens "mytoken1" has improved my life.

Last but not least, run [usethis::git_vaccinate()](https://usethis.r-lib.org/reference/git_vaccinate.html). It will prevent you leaking credentials, or useless files (`.DS_Store`), on GitHub.

## Run a full cycle

Even if your fantastic analysis project is still waiting for you to finish the *mise en place*, it might be good to run a full cycle to see whether things work.

-   Create a project with [`usethis::create_project("../toy")`](https://usethis.r-lib.org/reference/create_package.html).
-   Initiate git usage with [`usethis::use_git()`](https://usethis.r-lib.org/reference/use_git.html).
-   Add a text file containing, for instance "I will excel at git today!".
-   git commit this file. Use RStudio git tab, or [`gert::git_add()`](https://docs.ropensci.org/gert/reference/git_commit.html) then [`gert::git_commit()`](https://docs.ropensci.org/gert/reference/git_commit.html), or the command line... whatever you were taught and remember vaguely liking.
-   Create a GitHub repository with [`usethis::use_github()`](https://usethis.r-lib.org/reference/use_github.html), or GitHub Desktop.

Did it work? If not, turn to the docs, a search engine and your support network. If yes, continue!

## Initiate your project right

Once you have your project folder set up with `create_project()` for instance,

-   Initiate git usage with [`usethis::use_git()`](https://usethis.r-lib.org/reference/use_git.html).
-   Make sure `.gitignore` is correct: is there for instance a gigantic data file you work with locally but that you do not wish to commit to git, lest it is too heavy for git or GitHub? List it in `.gitignore`. You can use [`usethis::use_git_ignore()`](https://usethis.r-lib.org/reference/use_git_ignore.html) for that.

## Upload your project to GitHub

### First, where on GitHub do you want to upload your project?

Your personal account or an organization?

If you wish to upload it to an organization, be aware that it might fail if you don't have the [correct access level](https://docs.github.com/en/organizations/managing-peoples-access-to-your-organization-with-roles/roles-in-an-organization). In some GitHub organizations any member can create a repository, in others only owners can. Contact an organization owner if needed.

You might want to [*create* a GitHub organization](https://docs.github.com/en/organizations/collaborating-with-groups-in-organizations/creating-a-new-organization-from-scratch) for your project. Even with a single project in it, the big advantage is that you can better give access to your project to collaborators, with more granularity than for a repository hosted in a personal account.

### Private or public?

Do you want your repository to be public or private? Sometimes the choice is obvious, for instance your project is top secret or you really don't want to share it as is right now. Private repositories have a few less features than public repositories (for instance draft pull requests) depending on your GitHub subscription.

### `use_github()` (or GitHub Desktop)

Now run `usethis::use_github(organisation = "your-account-username-or-an-organization", private = TRUE)` with the correct arguments. After this you should see your project in a GitHub repository! :tada:

## Make commits as you work

Ideally, make a [commit](https://happygitwithr.com/git-basics.html#commits-diffs-and-tags) everytime you make a substantial, coherent set of changes. To encourage you, you could fill a jar of marbles, one marble per commit?

Realistically, at least make a commit every time you take a break, especially when leaving at the end of your working session.

Push often, each time you make a commit, with the RStudio git tab, the command line or [`gert::git_push()`](https://docs.ropensci.org/gert/reference/git_fetch.html), or GitHub Desktop.

If you feel icky submitting ugly commits to your pristine repo, think about the idea of branches. With usethis you

-   create one with [`gert::git_branch_create()`](https://docs.ropensci.org/gert/reference/git_branch.html),
-   experiment and make the ugly commits in there until the whole set of changes is good to go,
-   then open a "pull request" on GitHub with [usethis helpers](https://usethis.r-lib.org/articles/pr-functions.html),
-   then you can ["squash and merge"](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/merging-a-pull-request) it which means the whole thing becomes one single beautiful commit.

With GitHub Desktop you

-   [create a branch](https://docs.github.com/en/desktop/contributing-and-collaborating-using-github-desktop/making-changes-in-a-branch/managing-branches),
-   experiment and make the ugly commits in there until the whole set of changes is good to go,
-   then [open a "pull request" on GitHub](https://docs.github.com/en/desktop/contributing-and-collaborating-using-github-desktop/working-with-your-remote-repository-on-github-or-github-enterprise/creating-an-issue-or-pull-request),
-   then you can ["squash and merge"](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/incorporating-changes-from-a-pull-request/merging-a-pull-request) it which means the whole thing becomes one single beautiful commit.

## Keep track of your TODOS

Since you are working on GitHub, you could use the [issue tracker](https://docs.github.com/en/issues) of your repository to write down ideas! You can create labels, milestones (so you could procrastinate a lot here :sweat_smile:).

On Mastodon, Francisco Rodriguez-Sanchez [underlined how handy it can be to use GitHub for project management](https://mastodon.social/@frod_san@ecoevo.social/109677386173703866).

## Put the "Closed" sign before you leave

Have you done your last commit, last push and opened your last TODO issue? Have you looked over the history of what you did today and earlier in your GitHub repository?

If you intend to leave GitHub for a while,

-   make sure collaborators (or users of your package) have a good idea of how to contact you, and what maintenance is expected (for instance create a file `CONTRIBUTING.md` where you state the maintenance is stop and go).
-   set a [busy status on GitHub](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/personalizing-your-profile#setting-a-status).

## Procrastinate on your profile

A nice touch would be to make your [GitHub profile](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/about-your-profile) informative, setting a picture, description, and potentially a [GitHub README](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme). If you have GitHub repositories you are proud of, [pin them](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/pinning-items-to-your-profile) to your profile.

## Schedule training sessions?

Your work on checking your git/GitHub situation in RStudio, credentials and such will make your next restart easier. However practice will also help a lot!

Beside trying to get a bit more git into your daily work, you might want to schedule some sessions where you experiment with branches and pull requests in a toy or real repository you own, where you make you first pull request to an external repository. You could run these sessions with other (false) beginners!

## Conclusion

First of all, it is awesome you want to use git and GitHub (or GitLab or something else for that matter)! It is not an easy toolset to learn, so no wonder you can get frustated. Hopefully the tips in this post, which I'd summarize as "Remember happygitwithr.com, usethis and/or GitHub Desktop, Openscapes illustrated series; Be patient with yourself", can help a bit. Good luck!

## PS: improve your tooling?

-   Tip by Robert M Flight: [find a diff viewer you like](https://mastodon.social/@rmflight/109676738327559708).
-   Tip by Dave Braze: [experiment with other IDEs, use magit](https://mastodon.social/@davidbraze@mstdn.social/109676715427221572).

