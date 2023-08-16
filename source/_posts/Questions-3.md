---
title: 'Machine Learning Q&A Part III: SVM'
tags: Interview
categories: Machine Learning
mathjax: true
comments: true
abbrlink: c8f688ba
date: 2019/06/17 13:07:59
---
> Support Vector Machine

<!--more-->


## Support Vector Machine

### 1. SVM v.s. Logistic Regression

**SVM Optimization problem**:
$$
\max_{\beta_0,...\beta_p,\epsilon_1,..,\epsilon_n} M \\
s.t.  \sum_{j=1}^p \beta_j^2=1,  \quad (9.13) \\
 y_i(\beta_0+\beta_1x_{i1}+\beta_2x_{i2},...+\beta_px_{ip})>M(1-\epsilon_i) \quad \forall i=1,..,n.  \quad (9.14) \\
 \epsilon_i\geq0,\sum_{i=1}^p\epsilon_i \leq C, \quad (9.15)
$$
Rewrite the criterion (9.12)–(9.15) for fitting the support vector classifier $f(X) = β_0 + β_1X_1 + . . . + β_pX_p$ as 
$$
\min_{\beta_0,...,\beta_p}\left\{ \sum_{i=1}^n\max[0,1-y_if(x_i)]+\lambda\sum_{j=1}^p\beta_j^2 \right\}
$$

- λ is small: few violations to the margin ; high-variance, low-bias; $\Leftrightarrow$ small $C$;


**“Loss + Penalty” form**:
$$
\min_{\beta_0,...,\beta_p}\left\{ L(\mathbf{X},\mathbf{y},\beta)+\lambda P(\beta) \right\}
$$

- $L(\mathbf{X},\mathbf{y},\beta)$ : loss function
- $P(\beta)$: penalty function

**Ridge regression and the lasso**:
$$
L(\mathbf{X},\mathbf{y},\beta)=\sum_{i=1}^n \left( y_i-\beta_0-\sum_{j=1}^p x_{ij}\beta_j \right)^2 \\
P(\beta) = \sum_{j=1}^p \beta_j^2 \quad ridge \, regression \\
P(\beta) = \sum_{j=1}^p |\beta_j| \quad lasso
$$
**SVM**:  ***hindge loss***
$$
L(\mathbf{X},\mathbf{y},\beta)=\sum_{i=1}^n \max[0,1-y_i(\beta_0+\beta_1x_{i1}+,,,+\beta_px_{ip})]
$$
**Optimization problems of linear SVM and (regularized) LR**:
$$
\min_\beta \lambda||\beta||^2+\sum_{i=1}^n \max[0,1-y_i(\beta_0+\beta_1x_{i1}+,,,+\beta_px_{ip})] \\
\min_\beta \lambda||\beta||^2+\sum_{i=1}^n \log(1+\exp(1-y_i(\beta_0+\beta_1x_{i1}+,,,+\beta_px_{ip})))
$$
That is, they only differ in the loss function — **SVM minimizes hinge loss while logistic regression minimizes logistic loss.**

- Logistic loss diverges faster than hinge loss. So, in general, it will be more sensitive to outliers.
- Logistic loss does not go to zero even if the point is classified sufficiently confidently. This might lead to minor degradation in accuracy.

**Main Difference**:

- SVM try to maximize the margin between the closest support vectors while LR the posterior class probability. Thus, SVM find a solution which is as fare as possible for the two categories while LR has not this property.

<img src="./1.png" width="500" />

- LR is more sensitive to outliers than SVM because the cost function of LR diverges faster than those of SVM. So putting an outlier on above picture would give below picture:

<img src="./2.png" width="500" />

- Logistic Regression produces probabilistic values while SVM produces 1 or 0. So in a few words LR makes not absolute prediction and it does not assume data is enough to give a final decision. This maybe be good property when what we want is an estimation or we do not have high confidence into data.
  - In order to get discrete values **1 or 0** for the LR we can say that when a function value is greater than a threshold we classify as 1 and when a function value is smaller than the threshold we classify as 0.

**When to use which one?**

<img src="./4.png" width="500" />

### 2. What is a large margin classifier?

***Margin***: the smallest (perpendicular) distance from each training observation to a given separating hyperplane $\Rightarrow$  the minimal distance from the observations to the hyperplane.

***Maximal margin hyperplane***: the separating hyperplane that is farthest from the training observations.

- The maximal margin hyperplane is the separating hyperplane for which the *margin* is **largest**
- Overfitting when $p$ is large.

***Maximal margin classifier***: classify a test observation based on which side of the maximal margin hyperplane it lies. 

The **maximal margin hyperplane** is the solution to the optimization problem
$$
\max_{\beta_0,...\beta_p} M \\
s.t.  \sum_{j=1}^p \beta_j^2=1,  \quad (9.10) \\
 y_i(\beta_0+\beta_1x_{i1}+\beta_2x_{i2},...+\beta_px_{ip})>M \quad \forall i=1,..,n.  \quad (9.11)
$$

- The constraint in (9.11) in fact requires that each observation be on the correct side of the hyperplane, with some cushion, provided that **margin** $M$ is positive.)
- The constraint  in (9.10) makes sure the perpendicular distance from the i-th observation to the hyperplane is given by

$$
y_i(\beta_0+\beta_1x_{i1}+\beta_2x_{i2},...+\beta_px_{ip})
$$



### 3. Why SVM is an example of a large margin classifier?

***Support Vector Classifier (Soft Margin Classifier)***: Rather than seeking the largest possible margin  that every observation is not only on the correct side of the hyperplane but also on the correct side of the margin, we instead allow some observationsto be on the incorrect side of the margin, or even the incorrect side of the hyperplane.

​	**Optimization problem**:
$$
\max_{\beta_0,...\beta_p,\epsilon_1,..,\epsilon_n} M \\
s.t.  \sum_{j=1}^p \beta_j^2=1,  \quad (9.13) \\
 y_i(\beta_0+\beta_1x_{i1}+\beta_2x_{i2},...+\beta_px_{ip})>M(1-\epsilon_i) \quad \forall i=1,..,n.  \quad (9.14) \\
 \epsilon_i\geq0,\sum_{i=1}^p\epsilon_i \leq C, \quad (9.15)
$$

- ***Slack variables***: $\epsilon_1,..,\epsilon_n$ - allow individual observations to be on the wrong side of the margin or the hyperplane
  - $\epsilon_i=0$: the i-th observation is on the correct side of the *margin*
  - $\epsilon_i >0$: the i-th observation is on the wrong side of the *margin* $\Rightarrow$ i-th observation ***violated*** the margin.
  - $\epsilon_i >1$: the i-th observation is on the wrong side of the *hyperplane*
- Classify the test observation based on the sign of $f(x^∗) = \beta_0+\beta_1x_{1}^*+\beta_2x_{2}^*,...+\beta_px_{p}^*$.

The ***support vector machine (SVM)*** is an extension of the support vector classifier that results from enlarging the feature space using **kernels**.

### 4. SVM being a large margin classifier, is it influenced by outliers? 

Yes, if C is large, otherwise not

### 5. What is the role of C in SVM?

***Tuning parameter C***: $C$ bounds the sum of the $\epsilon_i$'s, and so it determines the number and severity of the violationsto the margin(and to the hyperplane) that we will tolerate. 

- ***budget*** for the amount that the margin can be violated by the $n$ observations.
- Generally chosen via *cross-validation*.
- $C$ controls the **bias-variance trade-off** of the support vector classifier.
  - C is small: highly fit to the data, fewer support vectors $\Rightarrow$  low bias , high variance;
  - C is large: margin wider, many support vectors $\Rightarrow$  high bias , low variance;

### 6. What is a kernel in SVM? Why do we use kernels in SVM?

**Kernel**:  Kernel is a function that quantifies the similarity of two observations. 

- ***Linear kernel***: $K(x_i,x_{i^{'}})=\sum_{j=1}^px_{ij}x_{i^{'}j}$ 
  - Linear kernel essentially quantifies the similarity of a pair of observations using **Pearson** (standard) correlation.
- ***Polynomial kernel*** of degree d: $K(x_i,x_{i^{'}})=(1+\sum_{j=1}^px_{ij}x_{i^{'}j})^d$
  - fitting a support vector classifier in a higher-dimensional space involving polynomials of degree $d$. 
- ***Radial kernel***: $K(x_i,x_{i^{'}})=\exp(-\gamma \sum_{j=1}^p(x_{ij}-x_{i^{'}j})^2)$  
  - Radial kernel has very *local* behavior: only nearby training observations have an effect on the class label of a test observation
    - If a given test observation $x^∗ = (x^∗_1 . . .x^∗_p)^T$ is far from a training observation $x_i$ in terms of ***Euclidean distance***; $\Rightarrow$  $ \sum_{j=1}^p(x_{ij}-x_{i^{'}j})^2 $ will be large $\Rightarrow$ $K(x_i,x_{i^{'}})=\exp(-\gamma \sum_{j=1}^p(x_{ij}-x_{i^{'}j})^2)$  will be very tiny. $\Rightarrow$ $x_i$ will play virtually no role in $f(x^∗)$.

***Support Vector Machine***: When the support vector classifieris combined with a non-linear kernel, the resulting classifier is known as a support vector machine.
$$
f(x)=\beta_0+\sum_{i \in S}^n \alpha_i K(x,x_i)
$$
In machine learning, a **“kernel”** is usually used to refer to the kernel trick, a method of ***using a linear classifier to solve a non-linear problem***.  The kernel function is what is applied on each data instance to map the original non-linear observations into a higher-dimensional space in which they become *separable*.

**Advantage of Kernel over enlarging the feature space using functions of the original features: **

- ***Computational***: one need only compute $K(x_i,x_{i^{'}})$ for all $\left(\begin{array}{c}n\\ 2\end{array}\right)$ distinct pairs $i, i^{'}$. This can bedone without explicitly working in the *enlarged feature space.* 
  - **Curse of dimensionality**: for some kernels, such as the radial kernel,  the feature space is implicit and infinite-dimensional.

### 7. Can we apply the kernel trick to logistic regression? Why is it not used in practice then?

While converting the primal SVM formulation into its dual form (which gives us the kernel version of the SVM),  we notice that one of the equations we get is,
$$
\beta=\sum_{i=1}^n \alpha_iy_ix_i
$$
This, in the kernelized version where the kernel $k$ has an implicit representation for points given by x $\rightarrow$ $\phi(x)$,
$$
\beta=\sum_{i=1}^n \alpha_iy_i\phi(x_i)
$$
Surprisingly, one can also get this form from the representer theorem [2]. This suggests something general about the classifiers we learn.

Now, let us get back to logistic regression, which is modeled as,
$$
p(y=1|x)=\frac{1}{1+\exp(-\beta^Tx)}
$$
First of all, let us map the x to the space of implicit representation of the kernel. So, our model in the implicit representation space would look like, 
$$
p(y=1|x)=\frac{1}{1+\exp(-\beta^T\phi(x))}
$$
Next, let us use the form of $\beta$, we observed from SVM and the representer theorem. This will give us,
$$
p(y=1|x)=\frac{1}{1+\exp(-\sum_{i=1}^n\alpha_iy_i\phi(x_i)^T\phi(x))} \\
=\frac{1}{1+\exp(-\sum_{i=1}^n\alpha_iy_iK(x_i,x))}
$$
This gives us the kernelized logistic regression model.

**Why is it not used in practice then?**: logistic regression with kernels is merely an **SVM** without maximum margins

### 8. How does the SVM Guassian kernel parameter $\lambda$ affect the bias/variance trade off?

**Larger RBF kernel bandwidths (i.e. smaller 𝛾γ) produce smoother decision boundaries because they produce smoother feature space mappings**. Smoother mappings produce simpler decision boundaries:

<img src="./5.png" width="700" />
$$
K(x_i,x_j)=\exp(-\lambda ||x_i-x_j||^2)
$$

- **Small $\lambda$:** a Gaussian with a *large variance* so the influence of $x_j$ is more, i.e. if $x_j$ is a support vector, the class of this support vector will have influence on deciding the class of the vector $x_i$ even if the distance between them is large $\rightarrow$ **Larger RBF kernel bandwidths** $\rightarrow$  smoother decision boundaries $  \rightarrow$  **high bias , low variance** 
- **Large $\lambda$:** variance is small  $\rightarrow$ support vector does not have wide-spread influence. Technically speaking $\rightarrow$ **low bias , high variance**.





**Ref**:

 [machine-learning-interview-questions](https://github.com/Sroy20/machine-learning-interview-questions)

[Support Vector Machine vs Logistic Regression](https://towardsdatascience.com/support-vector-machine-vs-logistic-regression-94cc2975433f)

[Kernel Functions](https://towardsdatascience.com/kernel-function-6f1d2be6091)

[What are kernels in machine learning and SVM and why do we need them?](https://www.quora.com/What-are-kernels-in-machine-learning-and-SVM-and-why-do-we-need-them)

[How can one use kernels (utilizing the kernel trick) in logistic regression?](https://www.quora.com/How-can-one-use-kernels-utilizing-the-kernel-trick-in-logistic-regression)

[What are C and gamma with regards to a support vector machine?](https://www.quora.com/What-are-C-and-gamma-with-regards-to-a-support-vector-machine)

