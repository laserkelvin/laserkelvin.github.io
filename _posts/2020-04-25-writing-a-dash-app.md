---
title: Replacing proprietary software with open-source
date: 2020-04-25 20:51
category: programming
tags: [front-ends, open-source, dash, web apps]
image: assets/blogs/dash-spectra.gif
layout: post
noindex: true
---

Up until today, one of the primary methods of analyzing older data (actually
stored on floppy disks!) in our lab was to use a specific Windows XP computer
that runs a specific version of National Instruments LabView (7.0), which has a
specific version of code that was written in the 2000's specifically for
this purpose.

You can probably see where this is going: when that computer fails because of a
bad hard drive, or something changes with that environment, no one is going to
be able analyze that data anymore! All the hours spent by numerous people
working to collect that data, it would be a shame for such high-quality
scientific results to disappear completely. The likely scenario is that we
would no longer be able to find that code, and we would not be able to obtain
that specific version of LabView ever again. [Forum posts like these are
probably not isolated
cases](https://forums.ni.com/t5/LabVIEW/Where-to-find-Labview-7-and-NI-DAQmx-compatibility-chart/td-p/3872921?profile.language=en),
where the code you need to run again is in dependency hell, and finding them is
harder than finding Carmen Sandiego. We should be able to implement the same
process in an open-source way.

The routine analysis can be broken down into steps:

1. Load a data file and do some archeology to parse the data out.
2. Display the raw frequency-domain spectrum for the user.
3. Process the spectrum with an FFT filter to remove types of noise.
4. Trial different filter parameters until we reach optimal signal-to-noise.

The filters have to be tweaked by hand, as experiment changes significantly day
to day, making it very difficult to build a pipeline to automate this.

## Requirements

So today I spent a good few hours building a modern piece of software that
performs the same functionality. I also want to let the more senior scientists
I work with whom may not be as tech-savvy to be able to process and inspect
data without too much fuss. Since we're starting from scratch, I wanted to make
sure that this time it's here to stay by adopting several steps to make sure
that the code is easily obtained, version controlled, and user-friendly.


Before
I started coding, there were several requirements I set for this project:

1. Open-source—I don't want to depend on proprietary software that may not be easily obtained in the future.
2. Interactivity—signal processing can be very visual and hands-on particularly with filter design, and so we need to be able to build in user interactivity.
3. Simple and intuitive—I didn't want to spend too long on this, and from a user perspective, the fewer moving parts the less chance the software will break!
4. Maintainable—same reason as above, and there is also a very large likelihood that someone else will have to modify/use the code, who may not be as coding-focused as I am.

So to summarize, I ended up developing a simple web app in Dash. These web apps
run in your browser (which every modern computer has), and therefore removes
any need overhead associated with other GUI frameworks (for example, Qt). For
what I'm doing, the cost of using something as powerful as Qt would be an
excessive amount of boilerplate, and I didn't want to spend so much time
writing the front end—because Dash/Flask is powered by web technology, a simple
CSS style sheet combined with DOM elements is sufficient to define your user
interface. The other issue with more complicated interfaces is that the user
needs to learn how to use your program: with my Dash app, what you see is what
you get.

## Starting a Dash project

This applies to every other coding project: never truly start from scratch.
When developing a package, there's a significant amount of things to implement,
like file structure, code organization, assets, and so on that can be
completely bypassed by using a _cookiecutter template_. With the power of
Google, you are seconds away from finding a template for the project you're
going to work on—in this case, I wanted a structured template for Dash that
includes all the boilerplate so I can dive right into writing my app.

For this, I found [chrisvoncsefalvay's cookiecutter repository](https://github.com/chrisvoncsefalvay/cookiecutter-dash) comprehensive; it includes support for a multipage Dash app and a good filestructure that should set you up well for small to medium sized projects. All you have to do is open up a terminal and run:

```bash
cookiecutter https://github.com/chrisvoncsefalvay/cookiecutter-dash
```

...and the prompt will ask you a series of questions to get your project
started. With that, you're ready to go! Here's a screencap of what to expect:

![screencap](/assets/blogs/dash-cookie.gif)

## Building a web app

Dash touts itself as a framework that allows you to create Web apps without
knowing any Javascript whatsoever, although to work with it well you do need to
know some basic HTML and CSS. The former defines the content and structure of
your page, while the latter adds flair and style to it. To have a working
webpage/Dash application, you nominally do not need any CSS although it
wouldn't look very attractive. As of current Dash versions, you can use either
remote CSS stylesheets (included as a URL), or local ones stored in the
`assets/*.css` folder.

Generally, the main file you'll be working in will be `app.py`—this will be the
code that defines your front-end through the use of HTML and Dash components,
and responses to user interactions and events with callbacks, both of which
constitute attributes of a `dash.Dash` object (usually created as a variable,
`app`). The former exists as the `layout` attribute, while the latter are
individual functions. The layout comprises the HTML, like this:

```python
import dash
import dash_html_components as html
import dash_core_components as dcc

app = dash.Dash("Dash Application")

app.layout = html.Div(
  children=[
      html.H1("Title of the page"),
      html.H2("Subtitle"),
      html.P("This is my Dash application!"),
      dcc.Graph(id="data-display")
    ],
  id="main-div"
)
```

The `id` argument for each component will correspond with the generated HTML;
when it comes to piping data in and out later (for callbacks) and for styling
with CSS.

What I like to do however, is to keep the analysis/scientific code separate
from the front-end. While it is easy to do so, you definitely do not want to
cram all of your code into `app.py`—instead, move the computation/parsing
functions and classes into other files. This actually brings us to one of the
trickier things to do with Dash, [which is passing data from functions to
functions to the
front-end](https://dash.plotly.com/sharing-data-between-callbacks). In my
application, I used the `dcc.Upload` component ([documentation
here](https://dash.plotly.com/dash-core-components/upload)) as the primary way
for a user to provide data to analyze. When a new file is uploaded, we update
two graphs on the front-end; one for the frequency and one for the time-domain
spectra. To do this, however, I wanted to keep the uploading/plotting as
separate functions and share the data between different functions. The solution
I had for this was to serialize/write the data to disk: keeping up with modern
libraries, [I used `xarray`](http://xarray.pydata.org/en/stable/) to organize
the parsed data and spectra, and save the data as NetCDF4 file kept in the
back. Whenever a function needs to access this data, we read in this cache. If
the data you're dealing with is simpler (i.e. strings and a few numbers) then
the [`dcc.Store`](https://dash.plotly.com/dash-core-components/store) component
will do the serialization for you.

Once we have our layout and processing functions in place, we need a way to
connect the two through Dash callbacks. In terms of implementation, a callback
function is a Python function decorated using `@app.callback`, where the
arguments correspond to where the results of the function are piped to, and
what object is emitting the event signal. Take this example:

```python
from dash.dependencies import Input, Output, State

@app.callback(
  Output("string-output", "children"),
  Input("hammer-button", "n_click")
)
def is_it_hammer_time(n_click, output_string):
  if n_click:
    output_string = "It is."
    return output_string
  else:
    raise dash.exceptions.PreventUpdate
```

Our "input" is the event signal generator, which outputs `n_click` as the
integer number of times the button has been clicked. If the output of this is
valid, then we set the text of "string-output" (an `html.P` component, for
example) to our desired text. The conditional statement raises a
`PreventUpdate` to stop the application from throwing an error when the button
has not been clicked.

And that's basically all the ingredients for a web application: a delicate
conversation between layout and callback events. Most of the pitfalls with Dash
have been relatively well documented, such as sharing data between parts of the
application and is a matter of finding the right posts in the Dash/Plotly
forums. The neat stuff is the strong synergy between Plotly and Dash, allowing
callbacks to be generated for plot interactions, which I use as the main form
of interaction for the user. Because I wanted to keep it intuitive and simple,
the FFT filter is activated when the user zooms into a portion of the
time-domain spectrum, and a third plot is generated corresponding to the
filtered data (see the gif at the top of the page). A user will then be able to
record the frequency and intensity of a signal by clicking on a point of the
spectrum in the processed panel. Here is the current version of the code:

![final](/assets/blogs/spectra-view-final.png)

It's not much to look at, but it's honest work.

## Conclusions

With the right amount of trial and error, we can replace a lot of codebase that
uses proprietary software, and in doing so make it future-proof and transparent
for other people to use. While a lot of paid software has a significant amount
of polish and user interaction research put into them, there is also a strong
case to be made for keeping code we use for analysis open and accessible: we
shouldn't be locked out of code because we don't have a MATLAB or LabView
license. All it takes is a little bit of tinkering, and we can implement fairly
high level "programs" for our—and maybe someone else's—day-to-day use.

## Extra Resources

[A link to the Github repository](https://github.com/laserkelvin/SpectraViewer)

[The Dash documentation and tutorials](https://dash.plotly.com/)

