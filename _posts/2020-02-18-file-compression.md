---
title: Compressing Files in Linux
date: 2020-02-18 17:45
category: linux 
tags: [workflows, linux]
teaser: A quick overview of file compression workflows with Linux
noindex: true
---

Here's a quick post about a topic I find myself revisiting every few months: how to compress large batches of files efficiently. As I perform lots of calculations on a computing cluster, I like to routinely back things up at around publication time: that way I can have access to the data at the point where my paper was submitted, for example, and come back to it at a later date.

Parallelization
---------------

Now, for UN*X users, a lot of people probably just use `tar` like so:

~~~ bash

tar -cf new_tarball.tar.gz file1 file2 file3

~~~

By itself, `tar` doesn't offer the best compression nor speed. There are a few more modern approaches that promise to be better. One approach, for example, are parallel implementations: every computer has multiple cores/threads these days, why not use them? Back in 2015, [this blog post](https://www.peterdavehello.org/2015/02/use-multi-threads-to-compress-files-when-taring-something/) showed a quick benchmark of how parallel implementations of common compression algorithms perform:

| Serial | Parallel |
|---|---|
| `gzip` | `pigz` |
| `bzip2` | `pbzip2` |
| `xz` | `pxz` |

With some pretty dated hardware, you could still get >3x faster. The simple thing about this is the plug-and-play nature of it all; you can get `pigz`, `pbzip2` through your package manager (e.g. `sudo apt install pigz`) or if you don't have `sudo` access, you could get the binaries from their websites or with `conda`:

~~~ bash

conda install -c bioconda pigz

~~~

Replace your regular `tar` command with this:

~~~ bash

tar -I pigz -cf tarball.tar.gz file1 file2 file3

~~~

Newer algorithms
----------------

Maybe not necessarily newer, but there are algorithms that promise to be faster and higher compression ratios than `tar` bundled with most systems. These include the ones mentioned in the table above, as well as `lrzip` or ["long range zip"](https://github.com/ckolivas/lrzip) which is apparently ~2x faster than `bzip2` thanks to its [LZMA](https://en.wikipedia.org/wiki/Lempel%E2%80%93Ziv%E2%80%93Markov_chain_algorithm) algorithm. For even beefier compression, there's [ZPAQ](http://mattmahoney.net/dc/zpaq.html) which, according to the author of `lrzip`, "extreme compression while taking forever".

As far as my experience goes, `bzip2` and `lrzip` are as far as I've had to take it. Given how problem dependent compression efficiency actually is, for your routine compression I would simply recommend `lrzip` thanks to its ease of use, speed, and compression.

Compressing lots of files
-------------------------

One scenario I find myself in often is taking many files and throwing them together - to do this quickly, I would do a first pass compressing the many files into a single, low compression tarball using one of the parallel algorithms. A second pass is done to further compress the file, where now the bottleneck isn't grabbing many files, it's simply to compress a single file.

So, something like this:

~~~ bash

tar -cf single_tarball.tar file1 file2 ... file999999

lrztar single_tarball.tar

~~~

Should generate a `single_tarball.tar.lrz` that is more compressed than the original, and because you're using parallelization to do many smaller files, ensures that your high compression algorithm is most spending its time working on a single file rather than many files.
