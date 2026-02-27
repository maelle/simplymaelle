---
title: "Zut, Git ! Porting {saperlipopette} to the terminal with Claude"
date: '2026-02-27'
tags:
  - teaching
  - git
slug: zut-saperlipopette-claude
---

A while ago, I decided to learn some Rust. 
I bought a book, opened it months later and started a small side-project: porting my R package saperlipopette to the terminal by writing a CLI with Rust. 
I then lost steam and discarded the project. 
Now, teaching more Git with [saperlipopette](https://docs.ropensci.org/saperlipopette) has me itching to do the same outside of R, to reach more learners. 
And in the meantime, even though I have [conflicting feelings](https://ropensci.org/blog/2026/02/26/ropensci-ai-policy/) about it, I realized that using an LLM, specifically Claude Code (Opus 4.6), would help me take my CLI over the finishing line!

## What my CLI already had

The CLI project already had a few exercises, all the “Oh shit, Git!” exercises from saperlipopette. 
It had a bad name: ohcrabgit, a pun based on “Oh crap, Git!” and on Rust’s logo being a crab. 
Definitely not something you want to be typing in a terminal again and again.

Its interface was the following:

- You’d create an exercise in a folder by typing a command in the terminal.
- You’d then open that folder, and read the file `instructions.txt` to know what to do, then the file `tip.txt` in case you needed hints.

That interface I wanted to keep, because it’s fairly simple and would work no matter what people usually use to work with Git. 
Surely they use Git somewhere where they can read text files.

It had some internal infrastructure: for instance a file called `git.rs` that had some utility functions such as `init_playground` or `create_branch`. 
It used the Rust crate git2 in particular, for the Git aspects.

Each line had been painstakingly written by me, with the help of a search engine and of the helpful error messages from the compiler. :sweat_smile:

## What I wanted for the CLI

I wasn’t so interested any more in writing Rust code myself. 
Maybe one day that’ll be relevant for my work, but it isn’t right now. 
I wanted to grow the CLI for it to match the features of saperlipopette: more exercises, and translations of the instructions/tips in French and Spanish. 
With that end product, I should be able to teach the same workshops as before, but using the CLI instead of saperlipopette if my audience were not R users only.

I also wanted to improve the formatting of the help, and the installation instructions.

The project went well! 
I have a Claude Code subscription, I call Claude from the terminal, from within Positron IDE. 
Note that I reached the rate limit of a session right before it was time for my Pilates class so it wasn’t too bad to just wait. :lotus_position:

Here’s what was produced in [zut source code](https://github.com/maelle/zut), and reviewed by me the Rust newbie, therefore not extremely deeply:

- Improvements of the help formatting. I pushed back on ANSI strings in the code, and asked for more accessible colors (not sure they’re the best choice). [Commit](https://github.com/maelle/zut/commit/a0be5dda82c93663904d07d31e22003f114e561e).
- I chose a better name for the CLI, the French curseword “zut” (“darn”). Claude did the renaming everywhere, which was especially lazy from me. [Commit](https://github.com/maelle/zut/commit/3679fbf50807a971125ab430f1441ed295d78e3c).
- The installation instructions are now to run `cargo install --git https://github.com/maelle/zut`, suggestion by Claude, since the audience for my tool should mostly be developers. I guess I’ll see what the pain points are when people do try to install it on different platforms... [Commit](https://github.com/maelle/zut/commit/92afceca8198233084d11bc914bdbbfe14d5bb06#diff-b335630551682c19a781afebcf4d07bf978fb1f8ac04c6bf87428ed5106870f5).
- The CLI now outputs instructions and tips in different languages based on the system locale, an environment variable, or a flag. I asked for the system locale to be the default. In that same stream of work, Claude added more unit tests, which I didn’t even request, and integration tests, which I did request after seeing the tests folder was empty. [Commit](https://github.com/maelle/zut/commit/30519918ebfb2316f47d25acb5945f869c938f48)
- All new exercises from saperlipopette were ported to zut in French, English and Spanish, and they were categorized in the help output as well as the docs (which at the moment is… the README). I tried running them, which lead to small tweaks (empty lines at the end of files) and bigger tweaks (for instance the git bisect exercise in saperlipopette has you run an R script, but it needed to become a shell script for it to make sense in zut). [Commit](https://github.com/maelle/zut/commit/4587ca6df38cb5371ceed45faf086f7161746dea).
- A bug fix, since zut only worked... from the folder containing its source. :facepalm: [Commit](https://github.com/maelle/zut/commit/a2d3eb9fb991a425ca9823132ddc4bebad889ab3).

## The state of zut

To install zut:

```sh
cargo install --git https://github.com/maelle/zut
```

To view help:

```
zut --help
```

whose output is:

```sh
Create example Git messes to solve, inspired by https://ohshitgit.com/

Usage: zut <EXO>[TARGET]. In the exercise folder, open instructions.txt.

Arguments:
  <EXO>
          Name of the exercise

          Possible values:
          - time-machine:       Oh shit, I did something terribly wrong, please tell me git has a magic time machine!?!
          - small-change:       Oh shit, I committed and immediately realized I need to make one small change!
          - latest-message:     Oh shit, I need to change the message on my last commit!
          - committed-to-main:  Oh shit, I accidentally committed something to main that should have been on a brand new branch!
          - committed-to-wrong: Oh shit, I accidentally committed to the wrong branch!
          - undo-commit:        Oh shit, I need to undo a commit from like 5 commits ago!
          - undo-file:          Oh shit, I need to undo my changes to a file!
          - split-changes:      Hey I'd like to split these changes to the same file into several commits!
          - clean-dir:          Hey, how do I remove all my debugging left-over stuff at once?
          - conflict:           Hey I'd like to see what merge conflicts look like!
          - rebase-i:           Hey I'd like to make my commits in a branch look informative and smart! (interactive rebase)
          - reset:              Hey I'd like to restart from scratch and reorganize my commits! (git reset --mixed)
          - bisect:             Hey I'd like to find which commit introduced a bug!
          - log-deleted-file:   I want to find which commit deleted a file!
          - log-deleted-line:   I want to find which commit deleted a line!
          - revparse:           I want to understand ancestry references like HEAD~5 and HEAD^^!
          - blame:              I want to find who added a specific line and when!
          - worktree:           I need to see what the project looked like at a certain version!

  [TARGET]
          Where to create the exercise directory. Default: temporary directory
          
          [default: tempdir]

Options:
      --lang <LANG>
          Language (default: auto-detected from system locale)
          
          [possible values: en, fr, es]

  -h, --help
          Print help (see a summary with '-h')

  -V, --version
          Print version

Examples:

  zut small-change  creates the small-change exercise folder in a temporary folder.
  zut latest-message ..  creates the latest-message exercise folder in the parent of the current folder.

Categories:

  Oh shit, Git!:
    time-machine, small-change, latest-message, committed-to-main,
    committed-to-wrong, undo-commit, undo-file

  Clean history:
    split-changes, clean-dir, conflict, rebase-i, reset

  Use history:
    bisect, log-deleted-file, log-deleted-line, revparse, blame, worktree
```


To create the time machine exercise in a temporary folder:

```sh
zut time-machine
```

The path to the folder is printed to screen so you can open it, read `instructions.txt` there and get working:
in that challenge a `git reset --hard` too many was run so you need `git reflog` to rescue an important commit.

To create the blame exercise in a folder above the current one, in French:

```sh
zut blame .. --lang fr
```

In that challenge, you need to find who committed a given line in a script.
Dr Jekyll or Mr Hyde? `git blame` will help!

Last but not least, if you try creating an exercise somewhere where it exists:

```sh
zut blame .. --lang fr
```

you get:

```sh
Exercise folder already exists: ../exo-blame — delete it or choose a different target.
```

## Conclusion: future work

Using Claude Code helped me port the features of the saperlipopette R package to the zut CLI. 
The next steps are for me to actually use zut more beyond the tests I did, to catch further problems. 
Say I need to teach [“Painlessly Improve Your Git History”](https://masalmon.eu/talks/2025-11-24-git-history/) again, I’d do all the exercises again in a row. I would do that in the human language of the workshop.

I could also improve the code of the CLI based on user’s feedback and on, say, feedback from Claude Code using the [posit-dev/critical-code-reviewer skill](https://github.com/posit-dev/skills/blob/97b0d388f24ccb500fd06ddd01116e427c5aa65f/posit-dev/critical-code-reviewer/SKILL.md?plain=1#L2) (thanks to [Mo](https://drmowinckels.io/) for telling me about it!).
The documentation could also use some work, compared to saperlipopette, but as a companion to workshops it might be ok.

The saperlipopette package itself has been submitted to [rOpenSci software peer-review](github.com/ropensci/software-review/issues/754) so I expect it to change for the better. 
When I need to port over those changes, depending on what they are I might resort to Claude Code again.
