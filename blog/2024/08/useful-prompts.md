@def title = "Handy language model prompts for productivity"
@def tags = ["llm", "coding", "assistant", "productivity"]
@def isblog = true
@def showall = true
@def rss_pubdate = Date(2024, 8, 25)
@def rss_title = "Handy language model prompts for productivity"
@def rss_description = "A letter to future to serve as a reminder to recenter myself throughout 2024."

# {{fill title}}

Lately, I've been increasingly using language models to help automate some of the nitty gritty stuff,
including setting up a workflow for [automating paper summarization](https://laserkelvin.github.io/research-paper-summaries), and
_some_ assistance with misc. coding and productivity tasks.

While the models that can be served on a client CPU/GPU have substantially improved in the last
two years, I've found that they're still not quite at the point where they are reliable enough
for me to pass more critical tasks. I still write all of my code, and even with RAG the paper
summaries can be either very impressive (pulling out specific figures and references), or on the
other hand, [not even wrong](https://en.wikipedia.org/wiki/Not_even_wrong). In the former case it might just be a matter
of coding style not meeting what I'd like, but I would like to still try and continue to use LLMs
where I find it comfortable to do so, which concretely translates to:

1. Sufficient automation/relieves my cognitive load. It doesn't make sense for me to
have an assistant where I don't trust their work, and I end up spending more time
turning something unusable into something useful.
2. Fits into my natural workflow: I code more or less exclusively in Neovim now, to
the point that using VSCode is actually quite jarring because of how I've managed
to tweak Neovim how I want it.\sidenote{1}{It's not just vim-bindings, it's the whole plugin ecosystem! More on that in a post sometime.}

## Docstring generation

Something that seems to fit squarely in this island of stability is docstring writing.
In Neovim, I have the `nvim-llama` plugin that will start an `Ollama` container when
I it, and I can access a chat prompt with the instruction fine-tuned Llama 3.1 8B.
My workflow consists of writing code, and using the following prompt:

> You are a helpful AI assistant that specializes in generating high-quality docstrings for Python code functions. 
> Your task is to create docstrings that are:
>
> - Accurate: Cover functionality, parameters, return values, and exceptions.
> - Concise: Brief and to the point, focusing on essential information.
> - Clear: Use simple language and avoid ambiguity.
> - Style: You must use NumPy style docstrings. When type hints/annotations are available, you must respect and use them.
> - Examples: Give examples in the docstring that demonstrate how the function should be used.
> - Generate docstring in this format:
> """<generated docstring>""".
>
> The code snippet is given below:

The prompt was largely taken from [arXiv:2405.10243v1](https://arxiv.org/html/2405.10243v1), but I've added my own
flair because I use NumPy style docstrings, and added the note for it to use type hints and annotations.

## Simple tasks

This kind of code generation is when it is faster to ask an LLM to do something
in a language I don't use every day: good examples are `regex` and some kinds of
shell tasks (weirdly, I never got too much into `bash` hacking).

> You are a senior software engineer with many decades of experience working
> in shell prompts. You have been asked to provide a short script in a specified
> language to perform one task. As part of your response, you must abide by the
> following rules:
>
> - Unless otherwise specified, use only the programming languages specified. Exceptions to this rule include cases like front and backend solutions (e.g. CSS/HTML/Javascript).
> - Your response should be to the point; do not use greetings, filler, or engage in friendly conversation.
> - Try and keep the complexity of your snippet relatively low: it should be short but easily grokked.
> - Provide your response in a code block format, with the language annotated.

