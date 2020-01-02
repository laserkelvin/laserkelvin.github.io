---
title: How I Write Scientific Manuscripts & Proposals
date: 2020-01-01 18:42
category: writing 
tags: [writing, workflows, manuscripts, science]
teaser: With so many ways to write, is there an optimum way to work on scientific papers, proposals, and reports?
---

For most, scientific writing is a chore: how many people actually enjoy writing
grant proposals, dealing with typesetting, or just sharing the details behind
an experiment? I, for one, actually quite like writing (perhaps for the wrong
reason) because it provides me an opportunity to play around with workflows,
discover new command line tools, or just mess around with typography and
typesetting. In this post, I'll cover some of the steps I take when writing; be
it for grants or for publications. This post will be split into three parts:
working solo or collaboratively, and themes common to both.

Common themes
-------------

The principles I try and adhere to is to separate formatting from content: I
will write in formats such as markdown and LaTeX, and use CSS and/or classes to
define all of the nitty-gritty details like margins and section headings. The
idea is to fuss minimally about formatting, which acts as my primary mode of
procastination (how do I make this look prettier?) and focus up on the actual
content. In this sense, I do find writing in markdown much better, although a
lot of the time I will end up writing at least some LaTeX within the markdown
document just to give some fine control over figure placement, etc.

I recommend deciding on a theme/format before you start depending on if you are
writing in LaTeX or markdown: look at the [ACS
LaTeX](https://ctan.org/pkg/achemso?lang=en) template for the former, and the
[Eisvogel](https://github.com/Wandmalfarbe/pandoc-latex-template) template for
the latter as starting points.

For making figures, I nearly exclusively use Python and ``matplotlib``. I will
seldom make minor edits in Inkscape, but in the spirit of reproducibility and
ease of later changes, the more you do in Python (either in a script or a notebook)
the less manual and sometimes difficult to reproduce changes you have to make
when some data or annotation needs to change.

I will usually use ``bibtex`` for references too: in more recent times
particularly with grant proposals, I will use the ``biblatex`` LaTeX package
with heavy tuning depending on the requirements of the funding agency. For
example, the National Science Foundation requests two separate bibliographies,
one for works supported by a previous grant, and those for the scientific
justification -- this is not easily done with conventional ``bibtex``, while it
is supported with the more finicky ``biblatex``.

Another referencing note is how citations/references are formatted. In most
cases it is simply changing ``biblatex`` options, but one of the largest
annoyances is getting the right journal abbreviations. In my workflow, I use
``JabRef`` to clean up my ``.bib`` file of duplicates, as well as abbreviate
journal names (although it could just as well be a job for ``sed``).

Working solo
------------

Working entirely by myself gives me complete freedom over what I can use, and
how I use them:

- Neovim for editing, along with some Vundle plugins that primarily improve quality of life 
- ``pdflatex`` or ``xelatex`` for typesetting 
- Pandoc for conversion and typesetting

While Overleaf (see next section) gives you the option of vim-like keybindings,
I prefer the ability to customize them as I do locally. Similarly, working
locally on Neovim lets me use plugins for [formatting tables
automatically](https://github.com/godlygeek/tabular), for [markdown
bindings](https://github.com/plasticboy/vim-markdown), and
[zen-mode](https://github.com/junegunn/goyo.vim).

For writing letters (like those that accommpany paper submission), I much prefer
using a [Pandoc letter template](https://github.com/aaronwolen/pandoc-letter) over
a Word document (shudder), whilst still keeping things like letter heads, signatures,
etc. accessible.


Working collaboratively
-----------------------

When I write with the intention to share or work with others, Overleaf is my
go-to option: everyone shares the same LaTeX environment with real-time
editing; no one needs to mess with downloading and installing ``tex`` packages,
and people who are more "TeX savvy" can be inclined to debug and fix
compilation errors for those less proficient (particularly those messy
bibliographies).

One of the great features of Overleaf is also version control on the cloud. With
the ability to save and label versions of text, I usually use this feature to
control iterations of manuscripts, as well as pre- and post-submission for publications
as a way to keep track of changes recommended by reviewers.

Personally, I'm not a fan of the "Track Changes" feature by Overleaf as it quickly
gets visually cluttered when multiple people are editing simultaneously.

