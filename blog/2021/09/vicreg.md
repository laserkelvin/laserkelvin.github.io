@def title = "Disentangling embeddings with self-supervised VICReg"
@def tags = ["representation learning", "self-supervised learning", "flux"]
@def isblog = true
@def showall = true

# {{fill title}}

Learning useful, *generalized* representations are universally important, however isn't a straightforward task. Conventional autoencoders and their variational counterparts will learn to produce embeddings that are useful, low dimensional representations, but they aren't guaranteed to fulfill things that we intuitively want to be included in these embeddings:

1. Implicit clustering of similar data&mdash;can we encourage models to learn an unbiased heuristic?\sidenote{unbiased}{What distance measure is the best for what you're doing? If we want to follow the spirit of unsupervised learning, we let the data speak for itself.}
2. Orthogonality&mdash;the data being encoded into each dimension should ideally be as uncorrelated with others as possible.\sidenote{pca}{Think principal components, and interpretability.}

## Contrastive learning

Conventionally, one way we can obtain good embeddings that can at least partially fulfill these points is through [contrastive learning](https://lilianweng.github.io/lil-log/2021/05/31/contrastive-representation-learning.html). The idea behind contrastive metric learning is simple: your model learns to ascribe a margin/difference between like and unlike data, which can be achieved with both labeled or unlabeled data. The problem with contrastively learning is typically the computational cost: for each training example, we need to traverse the dataset to find contrastive examples of varying difficulty.

## VICReg

This approach takes an implicit approach to contrastive metric learning, decomposing the mechanisms to getting data similarity and orthogonality into *variance*, *invariance*, and *covariance* metrics as a regularization measure.


The unfiltered notes can be [found here.](https://laserkelvin.github.io/ml-reviews/notes/vicreg)