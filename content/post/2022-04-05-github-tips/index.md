---
title: "8 (octo!) GitHub Tips"
date: '2022-04-05'
tags:
  - github
slug: github-tips
output: hugodown::hugo_document
rmd_hash: f67dd9df3221dcdf

---

I'm spending quite a lot of my working time on GitHub, so have taken some habits. Maybe some of them can be useful to you!

# 1: How to get started

I've never actually taught git and GitHub, but I like sharing these useful links:

-   \[Happy Git and GitHub for the useR\] by Jenny Bryan, the STAT 545 TAs, Jim Hester. It includes a big picture section ["Why Git? Why GitHub?"](https://happygitwithr.com/big-picture.html).

-   [Reflections on 4 months of GitHub: my advice to beginners](https://suzan.rbind.io/2018/03/reflections-4-months-of-github/) by Suzan Baert. It's pragmatic and encouraging.

-   [Excuse me, do you have a moment to talk about version control?](https://peerj.com/preprints/3159/) by Jenny Bryan.

# 2: Secure your account

Please use a password manager with some sort of cloud backup.

Enable [two factor-authentication for your GitHub account](https://docs.github.com/en/authentication/securing-your-account-with-two-factor-authentication-2fa/configuring-two-factor-authentication). It should not take much time. Make sure you store the recovery keys into your password manager. Also make sure your two factor-authentication app, if that's an app, has some sort of cloud backup.

If you interact with GitHub programmatically, say with the usethis package, refer to

-   [Managing Git(Hub) Credentials](https://usethis.r-lib.org/articles/git-credentials.html) in usethis docs;

-   [Managing GitHub credentials from R, difficulty level linux](https://blog.djnavarro.net/posts/2021-08-08_git-credential-helpers/) by Danielle Navarro (if you use Linux, that is!).

# 3: Refine GitHub interface with... Refined GitHub

[Refined GitHub](https://github.com/refined-github/refined-github) is a browser extension that adds nice features such as the default ordering of issues from most recently udpated to least recently updated. It's quite neat.

# 4: Make your profile informative

Try to fill your GitHub profile information (and to keep it up-to-date).

Furthermore,

-   [Pin your most important repositories](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/pinning-items-to-your-profile). They do not have to be *your* repositories, actually, they only need to be repositories you contributed to.

-   Make public the [organization memberships](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-user-account/managing-your-membership-in-organizations/publicizing-or-hiding-organization-membership) you want to show.

-   Add a [profile README](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-profile/customizing-your-profile/managing-your-profile-readme) (yes, I have still not done this myself).

# 5: Handle PR states and suggestions with available features

You can make [*draft* pull requests](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests#draft-pull-requests=) to indicate they are not ready yet. You can revert a pull request to the draft state. I find this most useful.

In the first comment of a pull request, if you add a line [`Fix #42`](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword=) (or some other [recognized keyword](https://docs.github.com/en/issues/tracking-your-work-with-issues/linking-a-pull-request-to-an-issue#linking-a-pull-request-to-an-issue-using-a-keyword=)), merging the PR will close issue 42. From the issue 42 itself, one will be able to see the PR is "linked" to it.

When reviewing a pull request, in comments you can make actual change suggestions, ["commit suggestions"](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/incorporating-feedback-in-your-pull-request) that the receiver can accept with a click. Using this instead of writing something à la `tipo -> typo` is quite handy. When you are on the receiving end, to [incorporate the feedback](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/reviewing-changes-in-pull-requests/incorporating-feedback-in-your-pull-request) you can either accept each suggestion individually or batch-accept them, but only from the files tab of the PR, not the main tab.

# 6: Custom watch repositories

Watching repositories can be very useful: your own repositories, a favorite package of yours, an important dependency of a project of yours, [Hacker News Daily Top 10](https://github.com/headllines/hackernews-daily/), etc.

When you watch a repository, you get notified of all issues, PRs, discussions, and also see all commits in your timeline. That last part might be a bit too much. Well, you can customize what to watch by [configuring your watch settings for an individual repository](https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github/setting-up-notifications/configuring-notifications#configuring-your-watch-settings-for-an-individual-repository=). I tend to subscribe to all issues, PRs and discussions, which I then tackle from my [notifications inbox](https://docs.github.com/en/account-and-profile/managing-subscriptions-and-notifications-on-github/viewing-and-triaging-notifications/managing-notifications-from-your-inbox#about-your-inbox=), one repository at a time.

# 7: Add your GitHub timeline to your RSS feed reader

At the very bottom of your GitHub timeline there is a link to your RSS feed, "Subscribe to your news feed" (it has a token in it so do not publicize it!). I have added mine to Feedly. Even if [GitHub has recently improved the timeline interface](https://github.blog/2022-03-22-improving-your-github-feed/), I like it to be grouped with other tech RSS feeds I subscribe to. That might change, though.

# 8: Subscribe to GitHub blog

I've also added GitHub blog to Feedly to be aware of new features in a less random way than on Twitter (I'm actively reducing my dependency on Twitter for crucial information).

# Conclusion

In this post I have shared eight tips based on my GitHub usage. Thanks to those who taught me some of these! Do *you* have some advice for GitHub users?

