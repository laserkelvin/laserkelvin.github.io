
@def title = "Contributing to open-source with `git` and Github"
@def tags = ["open-source", "programming", "git"]
@def isblog = true
@def showall = true

# Conceptual introduction to version control

### An introduction to scientists

I would say that the majority of modern science benefits significantly from open-source programs: routines that are freely available for research use, including a transparent code base allowing you to freely inspect and modify it for your own purposes. The great thing with this kind of paradigm is that you can benefit from open-source, and others can benefit from *your* code being open-source&mdash;anyone can stand on your shoulders! 😃

I wrote this post as a means to help scientists to open up their source, and as a general guide to how one can contribute code to an existing Github repository. I don't assume much knowledge, and I'll leave notes on the margin for additional context/explanations outside the main body of text. As always, you can always drop me a line on Twitter or email me!

This article is dedicated to Professor Timothy Barnum&mdash;you owe me a Dunkaccino

## What is `git`?

The first thing is to define some nomenclature: likely if you're looking at this post, you'll have heard `git` and Github used, and because the latter contains the former, maybe to an extent confused as to when the two can be used interchangably.

To clarify, `git`&mdash;as the type used implies&mdash;refers to a [program](https://en.wikipedia.org/wiki/Git): it's a program for managing software versions that was developed as a successor to `svm` by Linus Torvalds, who is the creator of Linux. As such, most modern UNIX based operating systems will come with `git` installed. Github, on the other hand, is a cloud platform now owned by Microsoft that serves as an online location where you can host your code repositories\sidenote{repo}{A repository can be thought of as a project&mdash;it should house all of the things related to that project, including documentation, a description of the code, citation information where relevant, and of course the codebase.}, and has grown now to include a lot of nifty automated workflows that lifts a lot of manual work from modern coding like testing, publishing, and hosting. You can use `git` independently of Github, and indeed there are alternatives: you can always [host your own repository service](https://gogs.io/), or use alternatives like [GitLab](https://gitlab.com).

So how does `git` benefit scientists? Primarily I see two aspects: first, you can manage&mdash;with relative ease\sidenote{lol}{At least better than copy pasting a file and appending `v6_final.py` everytime}&mdash;versions of the code you've written, be it scripts for analyzing data for a specific project, or a general purpose framework like `numpy`, which lends to scientific reproducibility; second, you can collaborate with your peers, your community, and potentially anyone in the world. The possibility that a project you came up with has the potential to grow into something even you couldn't fathom excites me.

Conceptually, `git` works by thinking of versions of your code as a tree that grows up:

![](/assets/posts/2021-10/github-tree-1.svg)

Here, the circles refer to *branches*\sidenote{1}{You can think of a branch as a spinoff of the code at a particular point in time: we isolate it so we can work on new things, or just to use it as a point of reference in time. The `main` branch is the typical default name, although in older repositories/`git` versions, it was called `master` branch.}, each of the rectangles contains a *commit*\sidenote{2}{A commit refers to the point codebase that is "saved" in time.}, and the arrows indicate the direction of travel between versions. 

In the example above, we started off with a codebase, and decided to start using `git` to track changes we make. We've since added a new feature, fixed what we thought was a typo that turned out to be correct, thereby reverting back to the new feature commit. The fact that this is progressing linearly in one direction means that we can keep track of *everything* we've done with the code.

Now say someone else wants to help develop your code, perhaps to implement a feature that was useful for their analysis and thought it was helpful for others as well. The proper way to do so involves making a *fork*:

![](/assets/posts/2021-10/github-tree-2.svg)

As seen in the diagram above, a fork\sidenote{3}{A fork is similar to "clone", but differs in that it recreates the repository entirely managed by you and you alone. Because you own the repository, you have full read/write access; conversely, cloning does not give you the same rights. This is important because it means you cannot easily mess up a common code base&mdash;there's shared responsibilities! If this doesn't make sense to you, move on and come back to this note after reading the whole post.} basically copies all of the code and history from the point in time you performed the action. 

![](/assets/posts/2021-10/github-tree-3.svg)

You then make the changes that you wanted, and you commit them ("Cool new feature"). At this point, you've diverged from the original repository by one commit. With vanilla `git`, if you know the author of the original code you could now *merge* the codebases, and bring the original up to speed with all of the new code. The common way to do so is called a *pull request*&mdash;it's named quite oddly, and so probably deserves some extra attention.\sidenote{merge-pr}{To clarify, merging branches is a general term for combining branches of code together. A pull request (PR) specifically asks for the owner of another repository to merge *your* changes to their branch.} The name originates from the `git` command, `git request-pull` which makes a bit more sense: you ask the owner of another repository to *pull* the changes [from your branch to theirs](https://git-scm.com/docs/git-request-pull).

# What about Github?

Now the example above would work, but it's not particularly convenient for you and your collaborator to have to work together to merge the codebase. Services like Github act as a third-party that can perform these tasks asynchronously, and with modern Github, automate a lot of work that might exist for particularly complex codebases.\sidenote{complexity}{As you might imagine, as your codebase gets increasingly complex, and if more people are involved, it's pretty hard to get everyone together in a room to agree on what changes can be merged and what needs to be revised!} So, in the interest of providing some practical elements to this post, we'll look at an example workflow of using `git` (the program) on your computer to make changes to an existing codebase/repository hosted on Github.

# Appendix

For your convenience, a table of terms used in `git` related version control.

| Term | Description |
|------|-------------|
| Branch | A spinoff of the codebase at a certain point in time |
| Commit | The act of *committing* your changes to the version history |
| Pull request | To ask the owner of another repository to pull changes from your repository |
| Merge | To combine two branches together |
| Revert | To go back to an earlier commit |
| Checkout | Generally used to revert changes on a file to the last commit. Alternatively with `-b`, change to a new branch |

