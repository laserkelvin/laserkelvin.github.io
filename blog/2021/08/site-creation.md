@def title = "Creating a site with Franklin"
@def tags = ["web", "css", "julia", "franklin"]
@def isblog = true

# Creating a site with Franklin

My old blog was built off of Jekyll. With limited time, I more or less Frankenstein'd my way into having a working website, but not particularly maintainable because a lot of the mechanisms were opaque to me. In this blog post, I make notes on how simple it was to personalize a `Franklin.jl` website. This is a handy post to read through if you are at the same level of expertise as I am: a minimal working knowledge of CSS and HTML, and some Julia.

\tableofcontents

## Why Franklin

When I found Franklin.jl, I was actually looking into building up a better workflow for technical blogging. While that's still ongoing\sidenote{pluto}{Pluto notebook support isn't native just yet, and there are numerous workarounds.} I figured playing around with a modern, math/code focused static page framework would be a fruitful experience.

So far, I'm loving it! Here's a few reasons:

- The documentation for Franklin is incredibly parseable, and the workflow/abstraction is highly intuitive.
- The templates springboard you into customization immediately: for the most part, all I had to do was touch up the CSS and HTML to have something feel very expressive of "me".
- I get to develop in Julia!

The last point actually means a lot&mdash;in the past I was only comfortable working on my Linux laptop for blogging because the environment was difficult to reproduce, and I never had the time to learn/figure it out for Windows. With Franklin, all you need is Git and a Julia installation!\sidenote{nowsl}{I don't even need WSL, which is pretty amazing.}

## Starting development

Getting it up and running for Windows was trivial:

1. Make sure Julia is added to `PATH` during installation
2. I use Powershell to navigate to the cloned repo and start Julia.\marginnote{os}{The great thing is that this work flow is mor or less the same on Linux as it is on Windows&mdash;I feel comfortable working on any computer I own.}
3. To spin up a new project and start developing, all I need to do is:

```julia
using Pkg

Pkg.add("Franklin")

using Franklin

newsite("."; template="tufte")
serve()
```

## Making it your own

Here are some of the personalizations I added to make the site more to my style. For all the CSS changes, I wrote to the `_css/adjust.css` to override whatever default settings.

### A color palette

Color is a huge thing for me, and for some of my previous stuff I really tend to go overboard: for this site I tried my best to tamper my enthusiasm and tried to optimize what *others see* while also trying to express individualism beyond stock colors.

For this, I used [Palettable](https://palettable.io), which helps generate nice, personalized color palettes.

### Fluid typography

One aspect that is important to me that may or may not work out of the box is responsive typography. A simple search lead me to an article on [CSS tricks](https://css-tricks.com/simplified-fluid-typography/) that provided a snippet of modern CSS that allows the text to dynamically adjust depending on the size of the screen, with minimum and maximum values (kind of interesting it took so long for a `clamp` function to be implemented):

```css
html {
    font-size: clamp(12px, 12px + 0.5vw, 5vw);
}
```

### Side/margin notes

The `tufte.css` stylesheet, and Edward Tufte's style more generally includes the extensive use of side and margin notes. Out of the box these aren't supported in Franklin, but just needs a small snippet to include in `config.md` that creates new shortcut commands for either format:

```md
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
```

These snippets are more or less copied from the `tufte-css` website, but modified to be used as two-argument macros in Franklin&mdash;I can add notes by specifying a tag, followed by the text I want on the side.


## To do

The sidebar `nav` contains mostly dead links right now, and I have two things to make things fit how I work:

1. A projects page that requires minimal HTML; script in Julia to populate predefined HTML/CSS grid/flex to template projects showcases, including links to arXiv/repos.
2. A CV builder scripted in Julia, that uses YAML and means I only need to store my information one format to generate HTML and printable PDF copies.
3. Add `font-awesome` icons into the mix; should just be simple CSS additions.

I haven't even touched on writing with code weaved in, nor rendering math&mdash;that's going to be a lot of fun!