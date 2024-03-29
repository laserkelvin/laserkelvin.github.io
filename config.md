<!--
Add here global page variables to use throughout your website.
-->
+++
author = "Kelvin Lee"
mintoclevel = 2

# Add here files or directories that should be ignored by Franklin, otherwise
# these files might be copied and, if markdown, processed by Franklin which
# you might not want. Indicate directories by ending the name with a `/`.
# Base files such as LICENSE.md and README.md are ignored by default.
ignore = ["node_modules/"]

# RSS (the website_{title, descr, url} must be defined to get RSS)
generate_rss = true
website_title = "laserkelvin.github.io"
website_descr = "A healthy mixture of professional and personal website and blog for Kelvin Lee"
website_url   = "https://laserkelvin.github.io/"
rss_full_content = true
isblog = false
+++

<!--
Add here global latex commands to use throughout your pages.
-->
\newcommand{\R}{\mathbb R}
\newcommand{\scal}[1]{\langle #1 \rangle}
\newcommand{\marginnote}[2]{
    ~~~
    <label for="mn-!#1" class="margin-toggle">&#8853;</label>
    <input type="checkbox" id="mn-!#1" class="margin-toggle"/>
    <span class="marginnote">!#2</span>
    ~~~
}
\newcommand{\sidenote}[2]{
    ~~~
    <label for="sn-!#1" class="margin-toggle sidenote-number"></label>
    <input type="checkbox" id="mn-!#1" class="margin-toggle"/>
    <span class="sidenote" id="sn-!#1">!#2</span>
    ~~~
}
\newcommand{\figenv}[4]{
    ~~~
    <figure class="fullwidth">
      <label for="fig-!#1" class="margin-toggle">&#8853;</label>
      <input type="checkbox" id="mn-!#1" class="margin-toggle"/>
      <span class="marginnote">!#2</span>
      <img src="!#3" alt="fig-!#1" style="!#4">
    </figure>
    ~~~
}
\newcommand{\gh}[1]{
    ~~~
    <a href="https://github.com/!#1"><i class="fa fa-github"></i><p>!#1</p></a>
    ~~~
}
\newcommand{\arxiv}[1]{
    ~~~
    <a href="https://arxiv.org/abs/!#1"><i class="ai ai-arxiv"></i><p>!#1</p></a>
    ~~~
}
\newcommand{\quote}[2]{
    ~~~
    <div class="epigraph">
      <blockquote>
        <p>!#1<p>
        <footer>!#2</footer>
      </blockquote>
    </div>
    ~~~
}
