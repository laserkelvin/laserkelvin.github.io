@def title = "Using LLMs for daily tasks"
@def tags = ["language models", "general", "zettelkasten", "second brain", "productivity", "neovim", "obsidian"]
@def isblog = true
@def showall = true
@def rss_pubdate = Date(2024, 12, 26)
@def rss_title = "Using LLMs for daily tasks"
@def rss_description = "Notes on how to configure Obsidian and Neovim for LLM use"

# {{fill title}}

I've recently had the time of day to start playing around with incorporating LLMs into
my usual workflows in earnest. Even though projects like `ollama` really lowered the
bar of hosting LLMs, it's taken me a while of playing around with a variety of frontends
and workflows such as Open WebUI to figure out exactly how exactly I want to use a locally
hosted model for myself.

The effective problem statement is something like this: I use `nvim` for coding, and actually
until quite recently, even for my notetaking with Telekasten.\sidenote{1}{Nothing was wrong
with telekasten as a plugin; I never really got the hang of retrieval (i.e. finding the
note I needed at the right time. Maybe it's more intuitive for others, but I never really
had the time to practice.)} I've since now moved to Obsidian for my notetaking, and it's
been a pretty good experience so far. Thus, if I want to use an LLM for productivity, I
would have to figure out how to serve both `nvim` and Obsidian.

## Setup

One of the big hurdles that I had to get over was actually
not messing around with `docker-compose`. Normally, this would be the way I would go because it's the self-hosting
way, but it introduced complexity that was really not needed to have to deal with ports,
uptime, availability, etc. Instead, I finally got over myself and just installed it with
the incredibly well packaged command given by the developers:

```console
curl -fsSL https://ollama.com/install.sh | sh
```

...this worked instantly on Manjaro, even creating a user group and installing it as a `systemctl`
service which meant that I don't have to deal with ports, `ollama` will always be running as a
background service\sidenote{2}{If you were ever worried about taking up GPU memory like I was,
`ollama` actually clears unused models from memory after five minutes. It'll also handle loading
and unloading weights when switching between models.}, and every "frontend" I use can just map
to the same server and model set. Clearly this was the design intention, but I liked to keep
my system/service relatively clean, and I tend to overthink things.

I was then able to just run `ollama pull <model_name>`.

## LLM use for coding

I've been using `qwen2.5-coder:7b-instruct-q5_K_S` as a coding assistant. I've found it to be
somewhat adequate, albeit a little verbose. It is actually significantly better than the
Llama 3.1 instruct models I've used previously for coding, especially in terms of the quality
of docstrings produced by the models.

In `nvim`, [`codecompanion.nvim`](https://github.com/olimorris/codecompanion.nvim/tree/main) has been a straightforward
install and configure to use the background `ollama` service, needing just a little bit of tinkering
to use `ollama` instead of the default ChatGPT remote service. Essentially, in addition to adding
it to `lazy.nvim` as a dependency, I added a `~/.config/nvim/lua/config/companion.lua` file that
then configures the "strategy" and "adapters":

```lua
require("codecompanion").setup(
  {
    strategies = {
      chat = {
        adapter = "ollama"
      },
      inline = {
        adapter = "ollama"
      }
    },
    adapters = {
    ollama = function()
      return require("codecompanion.adapters").extend("ollama", {
        name = "qwen2.5-coder",
        schema = {
          model = {
            default = "qwen2.5-coder:7b-instruct-q5_K_S"
          },
          num_ctx = {
            default = 64000
          }
        },
      })
    end,
    },
})
```

This uses the same `ollama` model for both inline and chat completion tasks. As mentioned already,
I've used this for docstring generation (The `<C-a>` keymap opens a chat window that I can just
paste a function's worth to generate at a time), and because the model is so lightweight, generation
is more or less instantaneous. Sometimes I do need to remind it to generate **NumPy** style docstrings,
as it generates something close but not exactly correct in the argument type descriptions.

## Obsidian Smart brain

For notetaking, doing retrieval augmented generation (RAG) has been **significantly** more straightforward for
information retrieval in my (limited) experience. I use `nomic-embed-text`, again provided by `ollama`, to
index notes, and use `llama3.2:3b` for chat generation. The small model is incredibly quick on my dGPU,
and will generate responses much faster than I can read them. The relevance and usefulness of the responses
can vary, but I've been pretty impressed so far when I ask questions about specific things I know exist in
my notes so far. This functionality is provided by [`obsidian-Smart2Brain`](https://github.com/your-papa/obsidian-Smart2Brain), which lets you
configure `ollama` as the LLM backend for embeddings and for generation.

## Conclusions

I'll follow up a bit more as I get the hang of the workflows, but I'm pretty keen to add more slash
commands and utilities to `nvim` for my coding productivity. As far as notetaking goes, the plugin
I'm using now doesn't seem to provide an avenue for *workflows* (i.e. agents) just yet, but I'm thinking
of a few areas beyond autotagging and RAG; how do I improve my **learning** with LLM assistants?
