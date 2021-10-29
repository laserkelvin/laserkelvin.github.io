
@def title = "Contributing to open-source with `git` and Github"
@def tags = ["open-source", "programming", "git"]
@def isblog = true
@def showall = true

# Contributing to open-source

**An introduction to scientists**

I would say that the majority of modern science benefits significantly from open-source programs: routines that are freely available for research use, including a transparent code base allowing you to freely inspect and modify it for your own purposes. The great thing with this kind of paradigm is that you can benefit from open-source, and others can benefit from *your* code being open-source&mdash;anyone can stand on your shoulders! 😃

I wrote this post as a means to help scientists to open up their source, and as a general guide to how one can contribute code to an existing Github repository.

## What is `git`?

The first thing is to define some nomenclature: likely if you're looking at this post, you'll have heard `git` and Github used, and because the latter contains the former, maybe to an extent confused as to when the two can be used interchangably.

To clarify, `git`&mdash;as the type used implies&mdash;refers to a [program](https://en.wikipedia.org/wiki/Git): it's a program for managing software versions that was developed as a successor to `svm` by Linus Torvalds, who is the creator of Linux. As such, most modern UNIX based operating systems will come with `git` installed.

So how does `git` benefit scientists? Primarily I see two aspects: first, you can manage&mdash;albeit not always easily&mdash;versions of the code you've written, be it scripts for analyzing data for a specific project, or a general purpose framework like `numpy`, which lends to scientific reproducibility; second, you can collaborate with your peers, your community, and potentially anyone in the world. The possibility that a project you came up with has the potential to grow into something even you couldn't fathom excites me.

Conceptually, `git` works by thinking of versions of your code as a tree that grows up:



## What is Github?