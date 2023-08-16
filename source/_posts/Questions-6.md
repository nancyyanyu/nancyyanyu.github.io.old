---
title: 'Machine Learning Q&A: Basic'
abbrlink: b89c7c35
date: 2019/06/17 17:49:31
tags: Interview
categories: Machine Learning
mathjax: true
comments: true
---

> linear regression

<!--more-->



### 1. Can you state Tom Mitchell's definition of learning and discuss T, P and E?

Mitchell (1997) provides the definition “A computer program is said to learn from **experience E** with respect to some class of **tasks T** and **performance measure P**, if its performance at tasks in **T**, as measured by **P**, improves with experience **E**.


### 2. What can be different types of tasks encountered in Machine Learning?

Classification, regression, Machine translation, Anomaly detection, Density estimation or probability mass function estimation

### 3. Consider linear regression. What are T, P and E?

 Task T : to predict y from $x$ by outputting $\hat{y} = \mathbf{w}^T\mathbf{x}$. 

Performance P:  compute the mean squared error of the model on the test set.
$$
MSE_{test}=\frac{1}{m}||\hat{y}^{test}-y^{test}||^2_2
$$
Experience E: training set $(X^{train}, y^{train})$.

### 4. What are supervised, unsupervised, semi-supervised, self-supervised, multi-instance learning, and reinforcement learning?

***Supervised learning:*** Training a model from input data and its corresponding labels.

***Unsupervised learning:*** Training a model to find patterns in a dataset, typically an unlabeled dataset.

***Semi-supervised learning:*** Training a model on data where some of the training examples have labels but others don’t. One technique for semi-supervised learning is to infer labels for the unlabeled examples, and then to train on the inferred labels to create a new model. Semi-supervised learning can be useful if labels are expensive to obtain but unlabeled examples are plentiful.

***Self-supervised learning:*** a relatively recent learning technique (in machine learning) where the **training data is  automatically labelled**. It is still supervised learning, but the datasets do not need to be manually labelled by human, but they can e.g. be labelled by finding and exploiting the relations (or correlations) between different input signals (input coming e.g. from different sensor modalities).

***Multi-instance learning:*** a type of supervised learning. Instead of receiving a set of instances which are individually labeled, **the learner receives a set of labeled *bags*, each containing many instances.** In the simple case of multiple-instance *binary classification*, a bag may be labeled negative if all the instances in it are negative. On the other hand, a bag is labeled positive if there is at least one instance in it which is positive. From a collection of labeled bags, the learner tries to either (i) induce a concept that will label individual instances correctly or (ii) learn how to label bags without inducing the concept.

 ***Reinforcement learning:*** A machine learning approach to **maximize an ultimate reward** through feedback (rewards and punishments) after a sequence of actions. For example, the ultimate reward of most games is victory. Reinforcement learning systems can become expert at playing complex games by evaluating sequences of previous game moves that ultimately led to wins and sequences that ultimately led to losses.

***Reinforcement learning*** is learning what to do---how to map situations to actions---so as to
maximize a numerical reward signal

### 5. Prove that for linear regression MSE can be derived from maximal likelihood by proper assumptions.

**Probabilistic assumption**: 

- Assume that the target variables and the inputs are related via the equation:

$$
y^{(i)}=\theta^Tx^{(i)}+\epsilon^{(i)}
$$

where $\epsilon^{(i)}$ is an error term that captures either unmodeled effects (such as if there are some features very pertinent to predicting housing price, but that we’d left out of the regression), or random noise.

- Assume $\epsilon^{(i)}$ are distributed IID (independently and identically distributed) according to a Gaussian distribution (also called a Normal distribution) mean zero and some variance $\sigma^2$ 

  - The density of $\epsilon^{(i)}$ is:
    $$
    p(\epsilon^{(i)})=\frac{1}{\sqrt{2\pi}\sigma}\exp \left(-\frac{(\epsilon^{(i)})^2}{2\sigma^2}\right)
    $$

  - This implies that:

  $$
  p(y^{(i)}|x^{(i)};\theta)=\frac{1}{\sqrt{2\pi}\sigma}\exp \left(-\frac{(y^{(i)}-\theta^Tx^{(i)})^2}{2\sigma^2}\right)
  $$

**Likelihood function**: 
$$
L(\theta)=L(\theta|\mathbf{X},\mathbf{y})=p(\mathbf{y}|\mathbf{X};\theta)
$$
$p(\mathbf{y}|\mathbf{X};\theta)$: This quantity is typically viewed a function of $\mathbf{y}$  (and perhaps X), for a fixed value of θ.

By the independence assumption on the  $\epsilon^{(i)}$’s (and hence also the  $y^{(i)}$’s given the  $x^{(i)}$ ’s), this can also be written:
$$
\begin{align}
L(\theta)&= \prod_{i=1}^n p(y^{(i)}|x^{(i)};\theta) \\
&=\prod_{i=1}^n \frac{1}{\sqrt{2\pi}\sigma}\exp \left(-\frac{(y^{(i)}-\theta^Tx^{(i)})^2}{2\sigma^2}\right)
\end{align}
$$
The principal of ***maximum likelihood*** says that we should choose $θ$ so as to make the data as high probability as possible  $\rightarrow$  maximize $L(θ)$.

Instead of maximizing $L(θ)$, we can also maximize any strictly increasing function of $L(θ)   $ $\rightarrow$  **log likelihood** $ℓ(θ)$:
$$
\begin{align}
ℓ(θ)=\log L(\theta)&=\log \prod_{i=1}^n p(y^{(i)}|x^{(i)};\theta) \\
&=\sum_{i=1}^n \log \frac{1}{\sqrt{2\pi}\sigma}\exp \left(-\frac{(y^{(i)}-\theta^Tx^{(i)})^2}{2\sigma^2}\right) \\
&= n\log\frac{1}{\sqrt{2\pi}\sigma}-\frac{1}{2\sigma^2} \sum_{i=1}^n (y^{(i)}-\theta^Tx^{(i)})^2
\end{align}
$$
Hense, maximizing $ℓ(θ)$ gives the same answer as minimizing 
$$
\frac{1}{2}\sum_{i=1}^n(h_\theta(x^{(i)})-y^{(i)})^2 =J(\theta)
$$
To summarize: Under the previous probabilistic assumptions on the data, least-squares regression corresponds to finding the maximum likelihood estimate of θ. Note also that, in our previous discussion, our final choice of θ did not depend on what was $\sigma^2$ , and indeed we’d have arrived at the same result
even if  $\sigma^2$ were unknown.



### 6. Derive the normal equation for linear regression.

Linear function:
$$
h_θ(x) = θ_0 + θ_1x_1 + θ_2x_2=θ^Tx
$$
Least-squares cost function:
$$
J(\theta)=\frac{1}{2}\sum_{i=1}^n(h_\theta(x^{(i)})-y^{(i)})^2
$$

$$
\begin{align}
\mathbf{X}&=\begin{bmatrix}- (x^{(1)})^T -  \\- (x^{(2)})^T - \\ ...\\- (x^{(n)})^T -\end{bmatrix} \\
\mathbf{y}&=\begin{bmatrix} y^{(1)} \\y^{(2)} \\... \\y^{(n)}\end{bmatrix}   \\
\mathbf{X}\theta-\mathbf{y}&=\begin{bmatrix} (x^{(1)})^T\theta-y^{(1)} \\(x^{(2)})^T\theta-y^{(2)} \\... \\(x^{(n)})^T\theta-y^{(n)}\end{bmatrix} \\
&=\begin{bmatrix} (h_\theta(x^{(1)})-y^{(1)} \\h_\theta(x^{(2)})-y^{(2)} \\... \\h_\theta(x^{(1)})-y^{(n)}\end{bmatrix}
\end{align}
$$
For a vector $z$, we have that: $z^Tz=\sum_i z^2_i$
$$
\frac{1}{2}(\mathbf{X}\theta-\mathbf{y})^T(\mathbf{X}\theta-\mathbf{y})=\frac{1}{2}\sum_{i=1}^n(h_\theta(x^{(i)})-y^{(i)})^2 =J(\theta)
$$
We know that: 
$$
\begin{align}
\frac{\partial f(A)}{\partial A^T}&=(\frac{\partial f(A)}{\partial A})^T \\
\frac{\partial \mathbf{y}^T\mathbf{A}\mathbf{x}}{\partial \mathbf{x}}&=\mathbf{y}^T\mathbf{A} \\
\frac{\partial \mathbf{y}^T\mathbf{A}\mathbf{x}}{\partial \mathbf{y}}&=\frac{\partial \mathbf{x}^T\mathbf{A}^T\mathbf{y}}{\partial \mathbf{y}}=\mathbf{x}^T\mathbf{A}^T \\
\frac{\partial \mathbf{x}^T\mathbf{A}\mathbf{x}}{\partial \mathbf{x}}&=\mathbf{x}^T\mathbf{A}^T +\mathbf{x}^T\mathbf{A}=\mathbf{x}^T（\mathbf{A}^T +\mathbf{A}）\\
\end{align}
$$
To minimize $J$, let’s find its derivatives with respect to $θ$:
$$
\begin{align}
\frac{\partial J(\theta)}{\partial \theta}&= \frac{\partial \frac{1}{2}(\mathbf{X}\theta-\mathbf{y})^T(\mathbf{X}\theta-\mathbf{y})}{\partial \theta}\\
&= \frac{1}{2}\frac{\partial (\theta^T\mathbf{X}^T\mathbf{X}\theta-\theta^T\mathbf{X}^T\mathbf{y}-\mathbf{y}^T\mathbf{X}\theta+\mathbf{y}^T\mathbf{y})}{\partial \theta} \\
&=\frac{1}{2}\frac{\partial (\theta^T\mathbf{X}^T\mathbf{X}\theta-\theta^T\mathbf{X}^T\mathbf{y}-\mathbf{y}^T\mathbf{X}\theta+\mathbf{y}^T\mathbf{y})}{\partial \theta} \\
&=\frac{1}{2} (\theta^T\mathbf{X}^T\mathbf{X}+\theta^T\mathbf{X}^T\mathbf{X}-\mathbf{y}^T\mathbf{X}-\mathbf{y}^T\mathbf{X}  )\\
&=\frac{1}{2}(\mathbf{X}^T\mathbf{X}\theta-2\mathbf{y}^T\mathbf{X}) \\
&=\mathbf{X}^T\mathbf{X}\theta-\mathbf{X}^T\mathbf{y}=0
\end{align}
$$
***Normal Equation***:
$$
\theta=(\mathbf{X}^T\mathbf{X})^{-1}\mathbf{X}^T\mathbf{y}
$$

### 7. Why is a validation set necessary?

Let's assume that you are training a model whose performance depends on a set of hyperparameters. In the case of a neural network, these parameters may be for instance the learning rate or the number of training iterations.

Given a choice of hyperparameter values, you use the **training** set to train the model. But, how do you set the values for the hyperparameters? That's what the **validation** set is for. You can use it to evaluate the performance of your model for different combinations of hyperparameter values (e.g. by means of a grid search process) and keep the best trained model.

But, how does your selected model compares to other different models? Is your neural network performing better than, let's say, a random forest trained with the same combination of training/test data? You cannot compare based on the validation set, because that validation set was part of the fitting of your model. You used it to select the hyperparameter values!

The **test** set allows you to compare different models in an unbiased way, by basing your comparisons in data that were not use in any part of your training/hyperparameter selection process.

### 8. What is the no free lunch theorem in connection to Machine Learning?

The ***no free lunch theorem*** for machine learning (Wolpert, 1996) states that, **averaged over**
**all possible data generating distributions, every classification algorithm has the**
**same error rate when classifying previously unobserved points**. In other words,
in some sense, **no machine learning algorithm is universally any better than any**
**other.** The most sophisticated algorithm we can conceive of has the same average
performance (over all possible tasks) as merely predicting that every point belongs
to the same class.

Fortunately, these results hold only when we average over all possible data
generating distributions. **If we make assumptions about the kinds of probability*
distributions we encounter in real-world applications,* then we can design learning
algorithms that perform well on these distributions.

This means that the goal of machine learning research is not to seek a universal
learning algorithm or the absolute best learning algorithm. Instead, *our goal is to*
*understand what kinds of distributions are relevant to the “real world” that an AI*
*agent experiences,* and what kinds of machine learning algorithms perform well on
data drawn from the kinds of data generating distributions we care about.

### 9. Discuss training error, test error, generalization error, overfitting, and underfitting.

***overfitting:*** the gap between the training error and test error is too large

***underfitting:*** the model is not able to obtain a sufficiently low error value on the training set

***training error:*** when training a machine learning model, we have access to a training
set, we can compute some error measure on the training set

***generalization error/test error:*** the expected value of the error on a new input. Here the expectation is taken across different possible inputs, drawn from the distribution of inputs we expect the system to encounter in practice

<img src="./1.png" width="600" />

### 10. Compare representational capacity vs. effective capacity of a model.

- **Representational capacity** - the functions which the model *can* learn; The model specifies which **family of functions** the learning algorithm can choose from when varying the parameters in order to reduce a training objective. 
- **Effective capacity** - in practice, a learning algorithm is not likely to find the *best* function out of the possible functions it can learn, though it can learn one that performs exceptionally well - those functions that the learning algorithm is capable of finding defines the model's *effective* capacity.

These additional limitations, such as the imperfection of the optimization algorithm, mean that the learning algorithm’s ***effective capacity*** may be less than the ***representational capacity*** of the model family

### 11. What is an ideal model? What is Bayes error? What is/are the source(s) of Bayes error occur?

***The ideal model***: is an oracle that simply knows the true probability distribution that generates the data.

- Even such a model will still incur some error on many
  problems, because there may still be some noise in the distribution. In the case
  of supervised learning, the mapping from $x$ to $y$ may be inherently stochastic,
  or $y$ may be a deterministic function that involves other variables besides those
  included in $x$. 

***Bayes error***: the lowest possible prediction error that can be achieved and is the same as irreducible error. ; The error incurred by an oracle making predictions from the true distribution p(x, y).

***Source(s) of Bayes error occur***:  noise in the distribution  if the process is random

### 12. What are nonparametric models? What is nonparametric learning?

Parametric models: learn a function described by a parameter vector whose size is finite and fixed before any data is observed (linear regression)

Non-parametric models: assume that the data distribution cannot be defined in terms of a finite set of parameters. But they can often be defined by assuming an infinite dimensional $\theta$ . Usually we think of $\theta$ as a function (nearest neighbor regression)



### 13. What is regularization? Intuitively, what does regularization do during the optimization procedure? 

***Regularization*** is any modification we make to a learning algorithm that is intended to reduce its generalization error but not its training error. 

We regularize a model that learns a function $f(x; θ)$ by adding a penalty called a **regularizer** to the cost function. **Expressing preferences for one function over another** implicitly and explicitly is a more general way of controlling a model’s capacity than including or excluding members from the hypothesis space. 



### 14. What is weight decay? What is it added?

***Weight decay*** is an additional term that causes the weights to exponentially decay to zero.

To perform linear regression with **weight decay**, we minimize a sum comprising both the mean squared error on the training and a criterion $J (w)$ that expresses a preference for the weights to have smaller squared L2 norm. Specifically,
$$
J(w) = MSE_{train} + λ\mathbf{w}^T\mathbf{w}
$$
Minimizing $J(w)$ results in a choice of weights that make a tradeoff between fitting the training data and being small. This gives us solutions that have a smaller slope, or put weight on fewer of the features. 



### 15. What is a hyperparameter? How do you choose which settings are going to be hyperparameters and which are going to be learnt?

**Hyperparameter**: Most machine learning algorithms have several settings that we can use to control
the behavior of the learning algorithm.

- The values of hyperparameters are not adapted by the learning algorithm itself 

Sometimes a setting is chosen to be a hyperparameter that the learning algorithm does not learn because it is **difficult to optimize** or it is not appropriate to learn that hyperparameter on the training set. This applies to all hyperparameters that control model capacity. If learned on the training set, such hyperparameters would always choose the maximum possible model capacity, resulting in **overfitting** 

### 16. Why is maximal likelihood the preferred estimator in ML?

The main appeal of the maximum likelihood estimator is that it can be shown to be the best estimator asymptotically, as the number of examples m → ∞, in terms of its rate of convergence as m increases

Under appropriate conditions, the maximum likelihood estimator has the property of :

- **consistency**: as the number of training examples approaches infinity, the maximum likelihood estimate of a parameter converges to the true value of the parameter. $\hat{\theta} \rightarrow^{n \rightarrow \infin} \theta$.
- **efficiency**: the Cramér-Rao lower bound (Rao, 1945; Cramér, 1946) shows that no consistent estimator has a lower mean squared error $Var(\hat{\theta}_n)$  than the maximum likelihood estimator

When the number of examples is small enough to yield overfitting behavior, regularization strategies
such as weight decay may be used to obtain a biased version of maximum likelihood that has less variance when training data is limited.

### 17. Under what conditions do the maximal likelihood estimator guarantee consistency?

1. The true distribution $p_{data}$ must lie within the model family $p_{model}(·; θ)$.
   Otherwise, no estimator can recover  $p_{data}$.
2. The true distribution $p_{data}$ must correspond to exactly one value of $θ$. Otherwise, maximum likelihood can recover the correct $p_{data}$ , but will not be able to determine which value of $θ$ was used by the data generating processing.

### 18. What do you mean by affine transformation? Discuss affine vs. linear transformation.

A function 𝑓 is linear if $𝑓(𝑎𝑥+𝑏𝑦)=𝑎𝑓(𝑥)+𝑏𝑓(𝑦)$ for all relevant values of 𝑎, 𝑏, 𝑥 and 𝑦.

A function 𝑔 is affine if $𝑔(𝑥)=𝑓(𝑥)+𝑐$ for some linear function 𝑓 and constant 𝑐.  Note that we allow 𝑐=0, which implies that every linear function is an affine function.

1. All linear transformations are affine transformations.
2. Not all affine transformations are linear transformations.
3. It can be shown that any affine transformation 𝐴:𝑈→𝑉 can be written as 𝐴(𝑥)=𝐿(𝑥)+𝑣0, where 𝑣0 is some vector from 𝑉 and 𝐿:𝑈→𝑉 is a linear transformation.



Discuss VC dimension.

The VC dimension measures the capacity of a binary classifier. The
VC dimension is defined as being the largest possible value of m for which there
exists a training set of m different x points that the classifier can label arbitrarily.