---
title: 'Machine Learning Q&A - Part IV: Clustering & Bayesian'
tags: Interview
categories: Machine Learning
mathjax: true
comments: true
abbrlink: 25b6d1fa
date: 2019/06/17 15:22:00
---

> Clustering/Bayesian

<!--more-->


## Clustering

### 1. Describe the k-means algorithm.

**K-means clustering** is a simple and elegant approach for partitioning a data set into K distinct, ***non-overlapping*** clusters. 

The idea behind **K-means clustering** is that a *good* clustering is one for which the ***within-cluster*** ***variation*** is as small as possible.

The **within-cluster variation** for cluster $C_k$ is a measure $W(C_k)$ of the amount by which the observations within a cluster differ from each other.


**Define the within-cluster variation**: ***Euclidean distance***:
$$
W(C_k)=\frac{1}{|C_k|}\sum_{i,i^{'}\in C_k}\sum_{j=1}^p(x_{ij}-x_{i^{'}j})^2
$$

- where $|C_k|$ denotes the number of observations in the kth cluster.
- The within-cluster variation for the kth cluster is *the sum of all of the pairwise squared Euclidean distances between the observations in the kth cluster*, divided by the total number of observations in the kth cluster.

**Objective funtion**:
$$
\min_{C_1,...,C_K}\left\{ \sum_{i=1}^K\frac{1}{|C_k|}\sum_{i,i^{'}\in C_k}\sum_{j=1}^p(x_{ij}-x_{i^{'}j})^2\right\}
$$

- partition the observations into K clusters such that the total within-cluster variation, summed over all K clusters, is *as small as possible*.

**Algorithm**

<img src="./1.png" width="600" />

In the algorithm above, $k$ (a parameter of the algorithm) is the number of clusters we want to find; and the cluster **centroids** $\mu_j$ represent our current guesses for the positions of the centers of the clusters. To initialize the cluster centroids, we could choose $k$ training
examples randomly, and set the cluster centroids to be equal to the values of these k examples. (Other initialization methods are also possible.)

The inner-loop of the algorithm repeatedly carries out two steps: 

- “Assigning” each training example $x^{(i)}$ to the closest cluster centroid $\mu_j$, and
- Moving each cluster centroid $\mu_j$ to the mean of the points assigned to it.

**Local optimum** : This means that as the algorithmis run, the clustering obtained will continually improve until the result no longer changes; the objective  will never increase.

- It is important to run the algorithm multiple times from different random initial configurations, because the results obtained will depend on the initial (random) cluster assignmentof each observation in Step 1 of Algorithm 10.1



### 2. What is distortion function? Is it convex or non-convex?

**Distortion function**: 
$$
J(c,\mu)=\sum_{i=1}^n ||x^{(i)}-\mu_{c^{(i)}}||^2
$$
$J$ measures the sum of squared distances between each training example $x^{(i)}$ and the cluster centroid $\mu_{c^{(i)}}$ to which it has been assigned. 

- k-means is exactly *coordinate descent* 坐标下降 on $J$. Specifically, the inner-loop of k-means repeatedly minimizes $J$ with respect to $c$ while holding $\mu$ fixed, and then minimizes $J$ with respect to $\mu$ while holding $c$ fixed. Thus,$J$ must monotonically decrease, and the value of $J$ must converge.



The distortion function $J$ is a ***non-convex*** function, and so coordinate descent on $J$ is not guaranteed to converge to the global minimum.



### 3. Describe the EM algorithm intuitively.





4. What is the Gaussian Mixture Model?
5. What are the two steps of the EM algorithm
7. Compare GMM vs GDA.



## Bayesian Machine Learning

### 1. What are the differences between “Bayesian” and “Freqentist” approach for Machine Learning?

The **Bayesian** approach differs from the standard ("**frequentist**") method for inference in its use of a *prior distribution* to express the uncertainty present before seeing the data, and to allow the uncertainty remaining after seeing the data to be expressed in the form of a *posterior distribution*.

Given a specific set of data, the **frequentist** believes that there is a true, underlying distribution from which said data was generated. The inability to get the exact parameters is a function of finite sample size. The **Bayesian**, on the other hand, think that we start with some assumption about the parameters (even if unknowingly) and use the data to refine our opinion about those parameters. 

- The **Bayesian** probability measures a "degree of belief". It pretty much matches our every-day intuitive understanding of probability,

- The **frequentists** interpretation needs some explanation. Frequentists can assign probabilities only to events/obervations that come from repeatable experiments. With "*probability of an event*" they mean the relative frequency of the event occuring in an infinitively long series of repetitions. For instance, when a frequentists says that the probability for "heads" in a coin toss is 0.5 (50%) he means that in infinititively many such coin tosses, 50% of the coins will show "head".

**Frequentist:** best suited to falsify a hypothesis
**Bayesian:** best suited to (re)allocate the credibility of a statement

#### Downsides of Frequentists

- Frequentists approach relies on data more than Bayesian as we totally ignore our knowledge or logical thinking which have been introduced in a form of prior probability.

- P-value does not provide the probability of your hypothesis to be collect. It only avoids the most extreme value that seems to be rare. It sometimes make the situation difficult as you may find it challenging to explain the actual meaning of value. Whereas, the posterior probability describes in percentage how likely your hypothesis is correct based on our prior knowledge.

#### Downsides of Bayesians

-  Bayesian Statistic requires more mathematical knowledge since the formula requires us to deduce two probability distributions.
- What if your prior has become meaningless as the logic we have is no longer valid?  (Some articles suggest that the prior at early stage can be any number as it can be updated as more information comes in)

### 2. When will you use Bayesian methods instead of Frequentist methods? 

Small dataset, large feature set

### 3. Compare maximum likelihood and maximum a posteriori estimation.

***MLE***:

Likelihood function: $P(X|\theta)$ 

MLE for θ, the parameter we want to infer: 
$$
\begin{align}
\theta_{MLE}&=\arg \max_\theta P(X|\theta) \\
&=\arg \max_\theta \prod_i P(x_i|\theta)
\end{align}
$$
As taking a product of some numbers less than 1 would approaching 0 as the number of those numbers goes to infinity, it would be not practical to compute, because of computation underflow. Hence, we will instead work in the log space, as logarithm is monotonically increasing, so maximizing a function is equal to maximizing the log of that function.
$$
\begin{align}
\theta_{MLE}&=\arg \max_\theta \log P(X|\theta) \\
&=\arg \max_\theta \log \prod_i P(x_i|\theta) \\
&=\arg \max_\theta \sum_i \log  P(x_i|\theta)
\end{align}
$$
To use this framework, we just need to derive the log likelihood of our model, then maximizing it with regard of θθ using our favorite optimization algorithm like Gradient Descent.



***MAP***:

MAP usually comes up in Bayesian setting. Because, as the name suggests, it works on a posterior distribution, not only the likelihood.

Bayes’ rule: 
$$
P(\theta|X)=\frac{P(X|\theta)P(\theta)}{P(X)} \\
\propto P(X|\theta)P(\theta)
$$

$$
\begin{align}
\theta_{MAP}&=\arg \max_\theta P(X|\theta) P(\theta) \\
&=\arg \max_\theta \log P(X|\theta) P(\theta) \\
&=\arg \max_\theta \log \prod_i P(x_i|\theta) P(\theta)\\
&=\arg \max_\theta \sum_i \log  P(x_i|\theta)P(\theta) 
\end{align}
$$

Comparing both MLE and MAP equation, the only thing differs is the **inclusion of prior P(θ)** in MAP. What it means is that, **the likelihood is now weighted with some weight coming from the prior**.



Let’s consider what if we use the simplest prior in our MAP estimation, i.e. ***uniform prior.*** This means, we assign equal weights everywhere, on all possible values of the θ. For example ,our prior P(θ) is $\frac{1}{6}$
$$
\begin{align}
\theta_{MAP} &=\arg \max_\theta \sum_i \log  P(x_i|\theta)P(\theta) \\
&= \arg \max_\theta \sum_i \log  P(x_i|\theta) const \\
&= \arg \max_\theta \sum_i \log  P(x_i|\theta) \\
&= \theta_{MLE}
\end{align}
$$






**Ref**:

 [machine-learning-interview-questions](https://github.com/Sroy20/machine-learning-interview-questions)

[CS229 Lecture notes: Unsupervised Learning, k-means clustering]([http://cs229.stanford.edu/notes-spring2019/cs229-notes7a.pdf](http://cs229.stanford.edu/notes-spring2019/cs229-notes7a.pdf))

[CS229 Lecture notes: Mixtures of Gaussians and the EM algorithm]([http://cs229.stanford.edu/notes-spring2019/cs229-notes7b.pdf](http://cs229.stanford.edu/notes-spring2019/cs229-notes7b.pdf))

[A Meaningless Debate: Frequentists vs Bayesians](https://medium.com/@yongddeng/a-meaningless-debate-frequentists-vs-bayesians-7317317b458f)

[Comparison of frequentist and Bayesian inference](https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading20.pdf)

[MLE vs MAP: the connection between Maximum Likelihood and Maximum A Posteriori Estimation](https://wiseodd.github.io/techblog/2017/01/01/mle-vs-map/)

