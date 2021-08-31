@def title = "Creating a site with Franklin"
@def tags = ["web", "css", "julia", "franklin"]
@def isblog = true

# Creating a site with Franklin

My old blog was built off of Jekyll. With limited time, I more or less Frankenstein'd my way into having a working website, but not particularly maintainable because a lot of the mechanisms were opaque to me.


## Why Franklin

When I found Franklin.jl, I was actually looking into building up a better workflow for technical blogging. While that's still ongoing\sidenote{Pluto notebook support isn't native just yet, and there are numerous workarounds.} I figured playing around with a modern, math/code focused static page framework would be a fruitful experience.

So far, I'm loving it! Here's a few reasons:

- The documentation for Franklin is incredibly parseable, and the workflow/abstraction is highly intuitive.
- The templates springboard you into customization immediately: for the most part, all I had to do was touch up the CSS and HTML to have something feel very expressive of "me".
- I get to develop in Julia!

The last point actually means a lot&mdash;in the past I was only comfortable working on my Linux laptop for blogging because the environment was difficult to reproduce, and I never had the time to learn/figure it out for Windows. With Franklin, all you need is Git and a Julia installation!\sidenote{I don't even need WSL, which is pretty amazing.}

## Starting development

Getting it up and running for Windows was trivial:

1. Make sure Julia is added to `PATH` during installation
2. I use Powershell to navigate to the cloned repo and start Julia.
3. To spin up a new project and start developing, all I need to do is:

```julia
using Pkg

Pkg.add("Franklin")

using Franklin

newsite("."; template="tufte")
serve()
```

## Making it your own

As I mentioned earlier
