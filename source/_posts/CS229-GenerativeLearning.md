---
title: "CS229 Note: Generative Learning"
abbrlink: '5576e748'
date: 2019/10/22 22:50:34
categories: 
- Machine Learning
- CS229
mathjax: true
comments: true
---

# Generative Learning algorithms

**Discriminative learning algorithms**: Algorithms that try to learn $p(y|x)$ directly (such as logistic regression), or algorithms that try to learn mappings directly from the space of inputs $X$ to the labels {0, 1}, (such as the perceptron algorithm)

<!--more-->

**Generative learning algorithms**: After modeling $p(y)$ (called the **class priors**) and $p(x|y)$, our algorithm can then use **Bayes rule** to derive the **posterior** distribution on $y$ given $x$:
$$
p(y|x)=\frac{p(x|y)p(y)}{p(x)}
$$
If were calculating $p(y|x)$ in order to make a prediction, then we don’t actually need to calculate the denominator, since
$$
\arg{\max{_y}p(y|x)}=\arg{\max{_y}\frac{p(x|y)p(y)}{p(x)}}=\arg{\max{_y}p(x|y)p(y)}
$$


## Gaussian discriminant analysis

**Assumption**: that $p(x|y)$ is distributed according to a **multivariate normal** distribution.

### The multivariate normal distribution

The **multivariate normal(Gaussian)** distribution in d-dimensions is parameterized by a **mean vector** $μ ∈ R^d$ and a **covariance matrix**  $\sum∈ R^{d\times d}$, where  $\sum≥ 0$ is <u>symmetric and positive semi-definite</u>. Also written “$\mathcal{N}(\mu, \Sigma)$”, its density is given by:
$$
p(x; μ,\Sigma)=\frac{1}{(2\pi)^{d/2}|\Sigma|^{1/2}}\exp{(-\frac{1}{2}(x-\mu)^T\Sigma^{-1}(x-\mu))}
$$
If $X \sim \mathcal{N}(\mu , \Sigma)$,
$$
E(X)=\int_{x}xp(x;\mu,\Sigma)dx=\mu\\
\mathrm{Cov}(X)=\Sigma
$$
<img src="./mn_1.png" width="600" />

covariance matrix  $\Sigma= I$,  $\Sigma= 0.6I$,  $\Sigma= 2I$





<img src="./mn_1.png" width="600" />
$$
\Sigma=\begin{bmatrix}1 & 0 \\0 & 1 \end{bmatrix} , \Sigma=\begin{bmatrix}1 & 0.5 \\0.5 & 1 \end{bmatrix} , \Sigma=\begin{bmatrix}1 & 0.8 \\0.8 & 1 \end{bmatrix} 
$$

### The Gaussian Discriminant Analysis model

#### 

