---
title: Why Variational Autoencoders Need to Reparameterize
date: 2020-03-11 21:23
category: machine learning 
tags: [deep learning, autoencoders, probabilistic models]
teaser: My somewhat belated epiphany on the reparameterization trick—learned through doing!
---

Today, I thought I had a stroke of brilliance by starting to develop a `Linear` layer in PyTorch that would have its parameters drawn from a Gaussian. My idea was to implement a Bayesian neural network, where the parameters of the network are treated as probability distributions, rather than just simple point estimates. In terms of Bayes rule:

$$
p(\theta \vert x) = \frac{p(x \vert \theta) p(\theta)}{p(x)}
$$

where $$p(\theta \vert x)$$ is the posterior distribution of parameters conditional on our data $$x$$; the distribution we want to ultimately draw samples from to evaluate our neural network. In my naivety I thought this would be as straightforward as my abstraction:  weights and biases had a $$\mu$$ and a $$\log \sigma$$ `Tensor` that would be the learned parameters of the layer:

```python
self.weight_mu = nn.Parameter(torch.Tensor(out_features, in_features))
self.weight_log_sigma = nn.Parameter(torch.Tensor(out_features, in_features))
if bias is None or bias is False:
    self.bias = False
else:
    self.bias = True
if self.bias:
    self.bias_mu = nn.Parameter(torch.Tensor(out_features))
    self.bias_log_sigma = nn.Parameter(torch.Tensor(out_features))
```

During the forward pass, we would use the `torch.distributions` module to draw samples based on these Gaussian parameters:

```python
def sample_parameters(self):
    # exponential on log sigma to enforce positive values
    # for the Gaussian width
    weights = torch.distributions.Normal(
        self.weight_mu, 
        torch.exp(self.weight_log_sigma)
        ).rsample()
    if self.bias:
        bias = torch.distributions.Normal(
            self.bias_mu, 
            torch.exp(self.bias_log_sigma)
            ).rsample()
    else:
        bias = None
    self.weights_values = weights
    self.bias_values = bias
    return weights, bias
```

The `weights` and `bias` are then fed into `F.linear`, which would then just evaluate it like a regular layer with the randomly drawn parameters.

---

When it came to training the network, I kept coming up to an error that was quite odd to me: it claimed that the 0th element of the loss tensor had no required gradients. Investigating for a bit, it wasn't immediately obvious to me why this was the case and I thought it was just a need to set `loss.requires_grad = True`. I was wrong!

Eventually, I went to the [`torch.distribution` docs](https://pytorch.org/docs/stable/distributions.html) and everything clicked into place: 

> It is not possible to directly backpropagate through random samples. However, there are two main methods for creating surrogate functions that can be backpropagated through.

So my loss function didn't know __where to backprop to__, because it doesn't know __how to evaluate the gradients needed to update $$\mu$$ and $$\log \sigma$$.__

I'd previously learned about variational autoencoders and reinforcement learning, but it was only after trying to do something dumb on my own that the relevance of everything fell into place. __These approaches were developed in order to circumvent the problem with using backprop with stochastically sampled parameters!__ Variational methods, for example, approximates $$p(\theta \vert x)$$ with another distribution $q(\theta \vert \theta')$ that has been reparametrized ($$\theta'$$) such that gradients _can_ be evaluated by minimizing the KL-divergence between $$p(\theta \vert x)$$ and $$q(\theta \vert \theta')$$: in other words, using $$\theta'$$ to make $$q(\theta \vert \theta')$$ look like the true posterior as much as possible. While it's not exact, it's about as good as we can get for now with respect to autograd. 

Conceptually, the code I wrote would work, albeit would need something like Monte Carlo integration to evaluate the full $\mu$ and $\log \sigma$ space including their gradients — a task that doesn't appear nearly as trivial anymore. I knew it was hard before, but it wasn't until I spent an hour working on this to realize how hard it was!

More papers to read:

[Variational Bayesian Inference with Stochastic Search](https://icml.cc/2012/papers/687.pdf)


