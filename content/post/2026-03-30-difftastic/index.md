---
title: "Better Git diff with difftastic"
date: '2026-03-30'
tags:
  - git
slug: difftastic
---

I'm currently on a quest to better know and understand treesitter-based tooling for R.
To make it short, treesitter is a tool for parsing code, for instance recognizing what is a function, an argument, a logical in a string of code.
With tools built upon treesitter you can [search](https://emilhvitfeldt.com/post/ast-grep-r-claude-code/), [reformat](https://posit-dev.github.io/air/), [lint and fix](https://jarl.etiennebacher.com/), etc. your code.
Exciting stuff, running locally and deterministically on your machine.

Speaking of "etc.", [Etienne Bacher](https://www.etiennebacher.com/) helpfully suggested I also look at treesitter-based tooling for _other languages_ to see what's still missing in our ecosystem.
This is how I stumbled upon difftastic, "a structural diff tool that understands syntax". :sparkles:
This means that difftastic doesn't only compare line or "words" but actual syntax by looking at lines around the lines that changed (by default, 3),
Even better, it understands R out of the box[^box].

[^box]: It's not every day we R developers look at the [homepage](https://difftastic.wilfred.me.uk/) of a tool and see the R logo among the logos of other languages!

_Many thanks to Etienne Bacher not only for making me discover difftastic but also for useful feedback on this post!_

## Installing difftastic

To install difftastic I downloaded a binary file for my system from the releases of the GitHub repository,
as [documented in the manual](https://difftastic.wilfred.me.uk/installation.html).

## difftastic on two files

You can run difftastic on two files, a bit like you would use the [waldo](https://waldo.r-lib.org/) R package on two objects.

Let's compare:

```r
a <- gsub("bad", "good", x)
```

to 

```r
a <- stringr::str_replace(x, "bad", "good")
```

respectedly saved in `old.R` and `new.R`.
The CLI is called difft not difftastic.
I use the "inline" display rather than the two columns default in order to save horizontal space.

```sh
difft old.R new.R --display inline
```

We'd get to this nice looking diff:

{{< figure src="oldnew.png" alt="diff of the two lines of code, where 'gsub' and ', x' are in red then 'strinrr::str_replace' and 'x' in green" >}}

The parentheses and `"bad"` and `"good"` arguments are ignored.

We can also get the JSON version of this diff, which is an unstable feature which usage requires setting an environment variable:

```sh
export DFT_UNSTABLE=yes
difft old.R new.R --display json
```

This gets us

```json
{"aligned_lines":[[0,0],[1,1]],"chunks":[[{"lhs":{"line_number":0,"changes":[{"start":5,"end":9,"content":"gsub","highlight":"normal"},{"start":23,"end":24,"content":",","highlight":"normal"},{"start":25,"end":26,"content":"x","highlight":"normal"}]},"rhs":{"line_number":0,"changes":[{"start":5,"end":12,"content":"stringr","highlight":"normal"},{"start":12,"end":14,"content":"::","highlight":"keyword"},{"start":14,"end":25,"content":"str_replace","highlight":"normal"},{"start":26,"end":27,"content":"x","highlight":"normal"},{"start":27,"end":28,"content":",","highlight":"normal"}]}}]],"language":"R","path":"content/post/2026-03-26-difftastic/new.R","status":"changed"}
```

Now, none of this isn't very useful because I would never compare files in this way...
I use version control!

## difftastic with Git

We can set difftastic as the external diff tool for Git globally or for the current project.

For instance with the gert R package, to set it locally:

```r
gert::git_config_set("diff.external", "difft")
```

If I want to use the inline display I'd set:

```r
gert::git_config_set("diff.external", "difft --display inline")
```

Then `git diff` will by default use difftastic.
Most interestingly for me, `git show --ext-diff` will use difftastic.
I never use `git diff` directly but I do look at more or less recent commits a lot.

Say I am interested in the [commit](https://github.com/r-lib/roxygen2/commit/7a1dd39866699a2b0a034bb15244c07698a1e2e7) that removed roxygen2's dependency on stringi, I'll run:

```sh
git show 7a1dd39866699a2b0a034bb15244c07698a1e2e7 --ext-diff
```
and get:

{{< figure src="strwrap.png" alt="diff where the parentheses of a nested call are nicely highlighted" >}}

This isn't spectacular because this is a small diff, but I enjoy the highlighting of the parentheses of the removed nested call, and of the logical.

## Cool features of difftastic

Building on two examples of the [difftastic homepage](https://difftastic.wilfred.me.uk/)...

### Ignoring formatting changes

Since formatters can so helpfully apply your formatting preferences,
reviewing formatting changes in a patch that's about something else entirely is useless and annoying.
Imagine having a function definition that fits on a single line, then adding one argument to it.

Going from

```r
f <- function(myarg1 = foo, myarg2 = bar) {}
```


to

```r
f <- function(
  myarg1 = foo,
  myarg2 = bar,
  myarg3 = baz
) {}
```

Because the definition is now longer than 80 characters, your formatter might switch the definition to be on multiple lines.
But the actually interesting change is the addition of one argument.

Native Git diff[^git] would show:

{{< figure src="args.png" alt="diff where all lines are highlighted because the function was reformatted, not only complemented with one argument" >}}

[^git]: To get the diff that Git would show me I ran `git diff --no-index old-args.R new-args.R --no-ext-diff`, cool trick I didn't know about! Very glad I didn't have to create a fake Git repo just for this. (`--no-ext-diff` because my diff in this repo would use difftastic by default!)

Git with difftastic would show:

{{< figure src="args-better.png" alt="diff where only the comma after `bar` and the line with the new argument are highlighted" >}}

The matching of delimiters is why I found the difftastic's display of the roxygen2 commit more pleasing.

### Matching delimiters in wrappers

The Git diff can look a bit ugly when you simply move code from one function to the other.

Say we go from

```r
f <- function() {
  1 + 1
}

```

to 

```r
f <- function() {
  g()
}

g <- function() {
  1 + 1
}

```

Git diff would show:

{{< figure src="wrappers-bad.png" alt="uncool diff that shows lines modified in both the wrapper and the function without matching delimiters" >}}

Whereas Git with difftastic would show:

{{< figure src="wrappers-good.png" alt="cool diff that shows `g` as a new function by highlighting its name and the left arrow, whereas the entire definiton of `f` is marked as changed." >}}

## Will I use difftastic?

I really like the concept behind difftastic and the few Git commits I looked at with it rendered nicely.
Now, what's [missing](https://github.com/Wilfred/difftastic#does-difftastic-integrate-with-my-favourite-tool) for me to use difftastic a lot is its integration with the tools where I actually use Git:

- Positron including the GitLens extension;
- GitHub Pull Request Files tab.

In any case, I'll continue learning about tools based on treesitter, some of which like [Air](https://posit-dev.github.io/air/) and [Jarl](https://jarl.etiennebacher.com/) I can already use directly from my IDE. :smile_cat: