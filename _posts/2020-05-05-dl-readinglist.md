---
title: My Reading List for Deep Learning
date: 2020-05-05 12:42
category: machine learning
tags: [deep learning, programming, autoencoders]
layout: post
noindex: true
---

This is a curated list of what I would recommend as resources for learning
about various aspects of deep learning, heavily inspired by [this Github
repository](https://github.com/floodsung/Deep-Learning-Papers-Reading-Roadmap),
although based on my own personal experience.

This reading list is relatively long, and I don't proclaim to have read every
single word on every single page. However, I am a firm believer of developing a
good foundation: given how expansive the current state of deep learning is, if
you're starting from scratch there is a lot you have to catch up with. My
advice is to take everything in strides, and learn what you need to when you
need to; _this is inevitable, but not insurmountable_!

## Building a good foundation

Whether you like it or not, deep learning requires a significant amount of
background knowledge in both linear algebra and statistics; you need a good
solid foundation before you can build a mansion. The former is a requirement
for understanding the core mechanics behind every model, and developing a good
intuition for linear algebra can provide you insight into some of the tricks
involved for some models (e.g. inner product decoders, the inception
architecture). Basic understanding of the latter is required for some of the
simpler tasks, for example classification. As we get to more complicated
problems, a background in Bayesian statistics is extremely helpful: these ideas
form the backbone for probabilistic modelling, which is used for generative
models—models that create new data based on what it has learnt.

For linear algebra, I don't actually recommend a mathematics textbook. Instead,
I recommend [_Mathematics for Quantum
Chemistry_](https://www.amazon.com/Mathematics-Quantum-Chemistry-Dover-Books/dp/0486442306);
don't let the title throw you off, because the first few chapters in what is
already a very short book gives you a quick primer and a good reference for
properties of linear algebra. Quantum chemistry actually uses a lot of the same
machinery as deep learning (there's a lot of matrix multiplication and
factorization), all of which was designed to solve approximations to the
Schrödinger equation. You will learn about expressing concepts as basis
functions, projections, and solving linear equations. The book is published by
Dover, and so is extremely cheap to pick up and good to have around!

For statistics, I generally avoid typical university textbooks that focus on
hypothesis testing (i.e.  $$p$$-values) that you might find common in
Psychology and Biology. The premise behind a lot of these ideas are
"frequentist", and in my humble opinion you are much better off thinking like a
Bayesian statistician instead (although not too much in fear of being paralyzed
by uncertainty). For this reason, I recommend [_Bayesian Data
Analysis_](https://www.amazon.com/Bayesian-Analysis-Chapman-Statistical-Science/dp/158488388X)
by Gelman, Carlin, Stern, and Rubin, and for a more applied book, [_Statistical
Rethinking: A Bayesian Course with Examples in R and
Stan_](https://xcelab.net/rm/statistical-rethinking/) by McElreath. The former
in particular sets you up to frame any problem in terms of likelihoods, and
provides case studies to understand how Bayesian statistics can help us solve
real-life problems and understand the role of uncertainty.

## Putting up the framework

There are two specific resources I would recommend that will set you up for
good: [_Deep Learning_](https://www.deeplearningbook.org/) by three giants of the
field: Ian Goodfellow, Yoshua Bengio, and Aaron Courville, and Andrew Ng's
course on deeplearning.ai.  The former provides an extremely solid basis and
theoretical underpinnings of the basics of deep learning, while Andrew Ng's
course is more pragmatic, teaching you how to implement these models from
scratch. A good book to accompany Andrew Ng's course is François Chollet's
[_Deep Learning with
Python_](https://www.amazon.com/Deep-Learning-Python-Francois-Chollet/dp/1617294438).
In both cases, there is a significant focus on Tensorflow and Keras (for
obvious reasons), although learning from _Deep Learning_ should provide you
enough abstraction to implement many of the basics.

In essence, the combination of these three materials is sufficient for you
to start playing around with deep learning models. Of course, this is
admittedly easier said than done because there's a significant amount of
reading, but your future self will thank you!

At this point, many of the latest concepts of deep learning come from academic
papers: unlike many other fields, virtually all of the material is available
without a pay-wall. We are fortunate that the machine learning and deep
learning researchers tend to upload a significant amount of their papers onto
ArXiv, which is not true for other disciplines (e.g. Chemistry, Physics,
Astronomy, etc.). I recommend finding something you're interested in solving,
and start working towards reading papers that provide solutions to those
problems. For example, if you're working with images, take a look at
convolutional models: AlexNet, LeNet, Inception, to name a few (in that order).
If you work on numerical/sequential data, check out recurrent neural networks.
[This Github
repository](https://github.com/floodsung/Deep-Learning-Papers-Reading-Roadmap)
provides paper highlights up until a few years ago, and covers the more seminal
papers for a lot of the current state-of-the-art. If I don't mention one of
those papers, it's probably going to be in that repository.

In the following sections, I'll be discussing more specific applications that
are not always systematically covered or make it into the mainstream media, but
are (I think) incredibly cool. This is my idea of a one-stop shop for more
forefront papers, that are not always related to one another.

__Bonus material:__ [This ArXiv paper](https://arxiv.org/pdf/1404.7828.pdf)
provides a fairly comprehensive historical overview of deep learning, dating
back to ideas from the early 20th century.

## Learning dynamics and capacity

One thing that I haven't found many posts or articles about is the general idea
of how much capacity neural networks are: it's not a straightforward question
to answer, and the literature is actually quite diverse on this matter. While
most people might dismiss as this "too theoretical", there are important implications
to be learned by understanding _how_ neural networks retain _what_ information. I
find this area quite interesting, because it certainly adds an "organic" component
to an optimization problem.

![estimating](/assets/blogs/reading/estimating_information_flow.png)

- [Estimating Information Flow in Neural Networks](http://people.lids.mit.edu/yp/homepage/data/dnn-infoflow.pdf)
- [The Capacity of Feedforward Neural Networks](https://www.math.uci.edu/~rvershyn/papers/bv-capacity-neural-networks.pdf)
- [On the Expressive Power of Deep Neural Networks](https://arxiv.org/pdf/1606.05336.pdf).

## Autoencoders

Autoencoders are a neat class of models that try to learn to extract useful
features in an unsupervised manner. The basic gist is an encoder model produces
an embedding that can be used by a decoder model to reproduce the inputs, and
by doing so, learns to essentially compress the important parts of an input
into a small feature vector. [This blog
post](https://lilianweng.github.io/lil-log/2018/08/12/from-autoencoder-to-beta-vae.html)
provides a comprehensive overview of variational autoencoders.

![vae](/assets/blogs/reading/vae.png)

- [Modular learning in neural networks, 1987](https://dl.acm.org/doi/10.5555/1863696.1863746)
- [Auto-encoding variational Bayes](http://arxiv.org/pdf/1312.6114); description of the variational autoencoder.
- [An Introduction to Variational Autoencoders](https://arxiv.org/pdf/1906.02691.pdf); a very recent introduction to VAE's.
- [$$\beta$$-VAE: Learning Basic Visual Concepts with a Constrained Variational Framework](https://openreview.net/pdf?id=Sy2fzU9gl); more recent developments in variational autoencoders.
- [Vector Quantized Variational Autoencoders](https://arxiv.org/pdf/1906.00446.pdf)
- [Temporal Difference Variational Auto-Encoder](https://arxiv.org/pdf/1806.03107.pdf)

While variational autoencoders are cool, they are typically limited by the fact
[that diagonal Gaussians do not make very good approximate to true posteriors
in many (maybe most)
cases](http://akosiorek.github.io/ml/2018/04/03/norm_flows.html). Normalizing
flows are a way to transform simple, easily computable distributions into
more expressive ones by means of a series of invertible transforms.

![normalizing](/assets/blogs/reading/normalizing.png)

- [Variational Inference with Normalizing Flows](https://arxiv.org/pdf/1505.05770.pdf); demonstration of the idea of normalizing flows.
- [Masked Autoregressive Flow for Density Estimation](https://arxiv.org/pdf/1705.07057.pdf)
- [Normalizing Flows: An Introduction and Review of Current Methods](https://arxiv.org/pdf/1908.09257.pdf)

## Probabilistic Models

The _Bayesian Data Analysis_ book should provide a good foundation for this
section: despite the section title, the focus is more on capturing model
uncertainty, _à la_ Bayesian statistics. Uncertainty quantification is an
essential part in rational decision making, adding to the overarching theme of
"making AI trustworthy" for policy making, self-driving cars, all that jazz.
This section is by no means comprehensive yet, and I intend to expand it more.

![ood](/assets/blogs/reading/ood.png)

- [Improving Out-of-Distribution Detection in Machine Learning Models](http://ai.googleblog.com/2019/12/improving-out-of-distribution-detection.html); blog post and paper about a simple way of estimating how likely your model is telling you an answer it doesn't know.
- [Robust Out-of-Distribution Detection for Neural Networks](https://arxiv.org/pdf/2003.09711.pdf)
- [Dropout as a Bayesian Approximation: Representing Model Uncertainty in Deep Learning](https://arxiv.org/pdf/1506.02142); the most cost-effective way of accounting for uncertainty—just keep dropout active during test time! Tends to underestimate uncertainty, however.
- [A Theoretically Grounded Application of Dropout in Recurrent Neural Networks](https://arxiv.org/pdf/1512.05287); extension of the idea above to recurrent models.


## Reinforcement Learning

The hot topic for deep learning, having neural networks teach themselves how to
solve problems through trial and error. This section is a little sparse for me
right now, but I will get to populating it soon.

![irl](/assets/blogs/reading/irl.png)

- [Apprenticeship learning via inverse reinforcement learning](https://dl.acm.org/doi/10.1145/1015330.1015430); not deep learning, but a very nice paper to read that forms the basis for reinforcement learning without well-defined rewards.
- [Image Augmentation is All You Need: Regularlizing Deep Reinforcement Learning from Pixels](https://arxiv.org/pdf/2004.13649.pdf); very recent paper on using simple augmentation to help policies learn tasks, rather than events.
- [Inverse Reinforcement Learning from Failure](http://www.cs.ox.ac.uk/people/shimon.whiteson/pubs/shiarlisaamas16.pdf); rather than have models learn only from perfect expert trajectories, have them learn from failures, too.
- [The OpenAI gym for reinforcement learning](https://spinningup.openai.com); good place to get started on _doing_ deep reinforcement learning.


## Graph models

Graph theory is a way of modelling diverse problems: for example, social
networks, circuitry, and structured data, and of course neural networks. There
is also a rise in popularity of [probabilistic
graphs](https://en.wikipedia.org/wiki/Graphical_model), because of how easy
they can potentially make understanding causal effects. In recent times, many
of the mainstream ideas in deep learning, such as convolutional and generative
models, have found analogous derivations in the graph neural network literature.

![vgae](/assets/blogs/reading/vgae.png)

- [Semi-Supervised Classification with Graph Convolutional Networks](https://arxiv.org/pdf/1609.02907.pdf); derivation of convolutional graph network models. [Accompanying blog post by Kipf](https://tkipf.github.io/graph-convolutional-networks/).
- [Variational Graph Auto-Encoders](https://arxiv.org/pdf/1611.07308.pdf); exactly as the title claims.
- [Graph Neural Networks: A Review of Method and Applications](https://arxiv.org/pdf/1812.08434.pdf); good place to start learning graph neural network models.
- [Topology Adaptive Graph Convolutional Networks](https://arxiv.org/pdf/1710.10370.pdf); efficient convolution in the vertex domain.
- [Pooling in Graph Convolutional Neural Networks](https://deepai.org/publication/pooling-in-graph-convolutional-neural-networks); necessary for downsampling, and previously not derived!

## Conclusions

For now, these are the resources I would go to for my deep learning fix. I'll
try and keep this post updated periodically, however you should also be doing
your own finding! One of the easiest ways would be to go through ArXiv, and
find papers that you find interesting. Given how expansive it is, and the fact
that tens to hundreds of new discoveries are being reported every week, my
recommendation again is to dive into specifics as you need to solve different
problems. It's very unlikely that you will be able to keep on top of
everything, and for your own sanity and mental well-being you should deal with
these papers and new ones at your own pace!

Feel free to reach out to me if you have questions, or if you think I missed
something and I should add this to the list! Also, please let me know if this
helped you out at all!

