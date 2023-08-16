---
title: 'Machine Learning Q&A Part I: Learning Theory & Model Selection'
tags: Interview
categories: Machine Learning
mathjax: true
comments: true
abbrlink: 6bd38994
date: 2019/06/14 22:57:50
---

> Learning Theory/bias-variance trade-off/Model and feature selection

<!--more-->

## Learning Theory

### 1. Describe bias and variance with examples.

***Variance***: refers to the amount by which $\hat{f}$ would change if we estimated it using a different training data set. ***more flexible statistical methods have higher variance***

- Explanation: different training data sets will result in a different $\hat{f}$. But ideally the estimate for f should not vary too much between training sets. However, if a method has high variance then small changes in the training data can result in large changes in $\hat{f}$

***Bias***: refers to the error that is introduced by approximating a real-life problem, which may be extremely complicated, by a much simpler model.

- Explanation: As we increase the flexibility of a class of methods, the bias tends to initially decrease faster than the variance increases. Consequently, the expected test MSE declines. However, at some point increasing flexibility has little impact on the bias but starts to significantly increase the variance. When this happens the test MSE increases.

***Decomposition***：The expected test MSE, for a given value $x_0$ can always be decomposed into the sum of three fundamental quantities: **the variance of $\hat{f}(x_0)$, the squared bias of $\hat{f}(x_0)$, and the variance of the error variance terms $\epsilon$.**
$$
\begin{align}
E(y_0-\hat{f}(x_0))^2=Var(\hat{f}(x_0))+[Bias(\hat{f}(x_0))]^2+Var(\epsilon)
\end{align}
$$
The overall expected test MSE can be computed by averaging $E(y_0-\hat{f}(x_0))^2$ over all possible values of $x_0$ in the test set.


<img src="./2.png" width="600" />



### 2. What is Empirical Risk Minimization? 

***ERM***: the function that minimizes loss on the training set. 

In many machine learning task, we have data $Z$ from some distribution $p$ and the task is to minimize the risk:
$$
R_(f)=E_{Z \sim p}[loss(f(Z),Z)]
$$
**Loss function**: In classification $Z = (X, Y )$  and we use 0/1 loss $loss(f(Z), Z) =
I_{f(X) \neq Y}$ , in regression $Z = (X, Y ) $and we use squared error $loss(f(Z), Z) = (f(X) − Y )^2$ and in density
estimation $Z = X$ and we use negative log likelihood loss $loss(f(Z), Z) = − \log f(X)$. We are interested in finding the optimal predictor
$$
f^*=\arg\min_f R(f)
$$
In practice, we compute the empirical risk:
$$
\hat{R}(f)=\frac{1}{n} \sum_{i=1}^n loss(f(X_i),Y_I)
$$
We choose the $\hat{f}$ that minimizes the empirical risk over some class $F$, such
as parametric models, histogram classifiers, decision trees or linear/polynomial functions, etc.
$$
\hat{f}^{ERM}=\arg \min_{f \in F} \hat{R}(f)
$$


### 3. Write the formulae for training error and generalization error.  Point out the differences.

***Generalization error*** is defined as the expected value of the error on a new input. Here the expectation is taken across different possible inputs, drawn from the distribution of inputs we expect the system to encounter in practice. [DL Book]

***Training error:*** when training a machine learning model, we have access to a training
set, we can compute some error measure on the training set called the training
error, and we reduce this training error.

In our linear regression example, we trained the model by minimizing the
*training error*:
$$
\frac{1}{m^{(train)}}||\mathbf{X}^{(train)}\mathbf{w}-\mathbf{y}^{(train)}||^2_2
$$
but we actually care about the *test error*:
$$
\frac{1}{m^{(test)}}||\mathbf{X}^{(test)}\mathbf{w}-\mathbf{y}^{(test)}||^2_2
$$
**Error v.s. Loss v.s. Risk**:

***Error*** is the difference between the actual / true value (𝜃) and the predicted / estimated value (𝜃̂ )
$$
Error=\theta-\hat{\theta}
$$
***Loss (𝐿)*** is a measurement of how well our model perform against your **training data**. 

- **Regression Losses**

  - **Mean Square Error/Quadratic Loss/L2 Loss**

  $$
  MSE=\frac{\sum_{i=1}^n(y_i-\hat{y}_i)^2}{n}
  $$

  - **Mean Absolute Error/L1 Loss**

  $$
  MAE=\frac{\sum_{i=1}^n|y_i-\hat{y}_i|}{n}
  $$

  - **Mean Bias Error**
    $$
    MBE=\frac{\sum_{i=1}^n(y_i-\hat{y}_i)}{n}
    $$

- **Classification Losses** 

  - **Hinge Loss/Multi class SVM Loss** 
    $$
    SVM Loss=\sum_{j \neq y_i} \max(0,s_j-s_{y_i}+1) \\
    L(\mathbf{X},\mathbf{y},\beta)=\sum_{i=1}^n \max[0,1-y_i(\beta_0+\beta_1x_{i1}+,,,+\beta_px_{ip})]
    $$
  
- **Cross Entropy Loss/Negative Log Likelihood**

$$
CrossEntropyLoss=-\left[y_i\log(\hat{y_i})+(1-y_i)\log (1-y_i)\right]
$$

***Risk*** is the average measure of loss, or expected loss, across your whole data distribution.
$$
R(\theta,\hat{\theta})=E(L(\theta,\hat{\theta}))
$$


***Empirical Risk:*** when we train our model, we do not have the full distribution of the data. This may be because some of our data is used for validation and testing, or that new data points are produced in real-time. The best we can do is to pick our training data in a random way and assume that our training  data is representative of the real data.

Therefore, because we don't have all the data, the best we can do is to minimize the empirical risk, from data that we do have (our training data), and use regularization techniques to generalize (i.e. avoid overfitting). This is why minimizing loss and minimizing empirical risk are roughly the same thing.



### 4. What is the bias-variance trade-off theorem?

***Bias-variance trade-off***: If our model is too simple and has very few parameters then it may have **high bias and low variance**. On the other hand if our model has large number of parameters then it’s going to have **high variance and low bias**. So we need to find the right/good balance without overfitting and underfitting the data.

<img src="./1.png" width="600" />

*Bias and variance using bulls-eye diagram:*

<img src="./3.png" width="300" />

***Underfitting*** happens when a model unable to capture the underlying pattern of the data $\Rightarrow$  high bias, low variance. 

***Overfitting*** happens when our model captures the noise along with the underlying pattern in data. It happens when we train our model a lot over noisy dataset $\Rightarrow$    low bias and high variance. 

<img src="./5.png" width="600" />



### 5. What is the VC dimension?



State the uniform convergence theorem and derive it.

What is sample complexity bound of uniform convergence theorem?

What is error bound of uniform convergence theorem?

From the bias-variance trade-off, can you derive the bound on training set size?What does the training set size depend on for a finite and infinite hypothesis set? Compare and contrast.What is the VC dimension for an n-dimensional linear classifier?How is the VC dimension of a SVM bounded although it is projected to an infinite dimension?Considering that Empirical Risk Minimization is a NP-hard problem, how does logistic regression and SVM loss work? 





## Model and feature selection

### 1. Why are model selection methods needed?

- ***Model Interpretability***：Irrelevant variables leads to unnecessary complexity in the resulting model. By removing these variables—that is, by setting the corresponding coefficient estimates
  to zero—we can obtain a model that is more easily interpreted. 

- We want to estimate the generalization performance, the predictive performance of our model on future (unseen) data.
- We want to increase the Prediction Accuracy by tweaking the learning algorithm and selecting the best performing model from a given hypothesis space.
- We want to identify the machine learning algorithm that is best-suited for the problem at hand; thus, we want to compare different algorithms, selecting the best-performing one as well as the best performing model from the algorithm’s hypothesis space.
- Fast (to train and test)
- Scalable (it can be applied to a large dataset)

The idea of model selection method is intuitive. It answers the following question:

> ***How to select the right input variables for an optimal model?***

**An Optimal model is a model that fits the data with best values for the evaluation metrics.**



(Feature selection simplifies a machine learning problem by choosing which subset of the available features should be used.)



### 2. How do you do a trade-off between bias and variance?

**Shrinkage methods:** By **constraining** or **shrinking** the estimated coefficients, we can often substantially reduce the variance at the cost of a negligible increase in bias.

**Bagging** and other **resampling techniques** can be used to reduce the variance in model predictions. 

### 3. Why is cross-validation required?

**Cross-validation**: can be used to estimate the test error associated with a given statistical learning method in order to evaluate its performance, or to select the appropriate level of flexibility.

Cross Validation is **a very useful technique for assessing the effectiveness of your model, particularly in cases where you need to mitigate overfitting.** You need some kind of **assurance that your model has got most of the patterns from the data correct, and its not picking up too much on the noise, or in other words its low on bias and variance.**

Evaluation of residuals only gives us an idea about how well our model does on data used to train it. So, the problem with this evaluation technique is that **it does not give an indication of how well the learner will generalize to an independent/ unseen data set.** Getting this idea about our model is known as Cross Validation.

It is **also of use in determining the hyper parameters of your model**, in the sense that which parameters will result in lowest test error. 

### 4. Describe different cross-validation techniques.

#### What is hold-out cross validation? What are its advantages and disadvantages?

**Hold-out cross validation**: 

1. Randomly dividing the available set of observations into two parts, a **training set** and a **validation set** or hold-out set. 
2. The model is fit on the training set, and the fitted model is used to predict the responses for the observations in the validation set. 
3. The resulting validation set error rate—typically assessed using MSE in the case of a quantitative response—provides an estimate of the test error rate.

**Advantage**: this method doesn’t take any overhead to compute and is better than traditional validation

**Disadvantage**: 

- **High variance**: because it is not certain which data points will end up in the validation set and the result might be entirely different for different sets.
- Removing a part of it for validation poses a problem of **underfitting**

#### What is k-fold cross validation? What are its advantages and disadvantages?

**K-fold cross validation**:

1. Randomly k-fold CV dividing the set of observations into k groups, or folds, of approximately equal size. 
2. The first fold is treated as a validation set, and the method is fit on the remaining k − 1 folds. 
3. The mean squared error, MSE1, is then computed on the observations in the held-out fold. This procedure is repeated k times; each time, a different group of observations is treated as a validation set. 
4. This process results in k estimates of the test error, MSE1,MSE2, . . . ,MSEk. 
5. The k-fold CV estimate is computed by averaging these values,

$$
\begin{align}
CV_{(k)}=\frac{1}{k}\sum_{i=1}^kMSE_i
\end{align}
$$

**Advantage**: significantly ***reduces bias*** as we are using most of the data for fitting, and also significantly ***reduces variance*** as most of the data is also being used in validation set. 

**Disadvantage**:  the training algorithm has to be rerun from scratch *k* times, which means it takes *k* times as much computation to make an evaluation. 

#### What is leave-one-out cross validation? What are its advantages and disadvantages?

**Leave-one-out cross validation**: leaves 1 data points out of training data as validation set. The statistical learning method is fit on the n − 1 training observations, and a prediction $y_1$ is made for the excluded observation, using its value $x_1$. and then the error is averaged for all trials, to give overall effectiveness.

Since $ (x_1, y_1)$ was not used in the fitting process, $MSE_1 =(y_1 − \hat{y}_1)^2$ provides an approximately ***unbiased*** estimate for the test error.But even though $MSE_1$ is unbiased for the test error, it is a poor estimate
because it is ***highly variable***, since it is based upon a single observation $ (x_1, y_1)$.

**Advantages and disadvantages**:

- k-Fold more biased than LOOCV; k-Fold less variance than LOOCV

- When we perform LOOCV, we are in effect averaging the outputs of n fitted models, each of which is trained on an almost identical set of observations; therefore, these outputs are highly (positively) correlated with each other.
- very expensive to compute

### 5. Why is feature selection required?

- **Reduces Overfitting**: Less redundant data means less opportunity to make decisions based on noise.

-  **Improves Accuracy:** Less misleading data means modeling accuracy improves.

-  **Reduces Training Time**: fewer data points reduce algorithm complexity and algorithms train faster.

### 6. Describe some feature selection methods.

- **Filter Methods:** apply a statistical measure to assign a scoring to each feature. The features are ranked by the score and either selected to be kept or removed from the dataset.
  - distance metrics, correlation, mutual information, and consistency metrics
- **Wrapper Methods:** consider the selection of a set of features as a search problem, where different combinations are prepared, evaluated and compared to other combinations. A predictive model is used to evaluate a combination of features and assign a score based on model accuracy.
  - Best Subset, forward and backward, recursive feature elimination
- **Embedded Methods:** learn which features best contribute to the accuracy of the model while the model is being created.
  - regularization methods: LASSO, Elastic Net and Ridge Regression

### 7. What is forward feature selection method? What are its advantages and disadvantages?

**Forward stepwise selection** starts with the intercept, and then sequentially adds into the model the predictor that most improves the ﬁt.

**Advantages and Disadvantages**:

- **Computational**: for large p we cannot compute the best subset sequence, but we can always compute the forward stepwise sequence
- **Statistical**: a price is paid in variance for selecting the best subset of each size; forward stepwise is a more constrained search, and will have lower variance, but perhaps more bias

<img src="./6.png" width="600" />

### 8. What is backward feature selection method? What are its advantages and disadvantages?

**Backward-stepwise selection** starts with the full model, and sequentially deletes the predictor that has the least impact on the ﬁt. The candidate for dropping is the variable with the smallest Z-score

**Advantages and Disadvantages**:

- Backward-stepwise selection can only be used when N>p, while forward stepwise can always be used.

- Like forward stepwise selection, backward stepwise selection is not guaranteed to yield the best
  model containing a subset of the p predictors.

<img src="./7.png" width="600" />



### 9. What is filter feature selection method and describe two of them?

**Filter Methods:** apply a statistical measure to assign a scoring to each feature. The features are ranked by the score and either selected to be kept or removed from the dataset.

- [**Chi-squared**](http://nlp.stanford.edu/IR-book/html/htmledition/feature-selectionchi2-feature-selection-1.html) *:*Chi-square test is used for categorical features in a dataset. We calculate Chi-square between each feature and the target and select the desired number of features with best Chi-square scores. to evaluate how likely it is that any observed difference between the sets arose by chance or if the association between two categorical variables of the sample would reflect their real association in the population.

- [**Correlation**](http://www.ime.unicamp.br/~wanderson/Artigos/correlation_based_feature_selection.pdf) **:** The Correlation Feature Selection (CFS) measure evaluates subsets of features on the basis of the following hypothesis: "Good feature subsets contain features highly correlated with the classification, yet uncorrelated to each other

- [**Entropy**](https://hal.archives-ouvertes.fr/hal-00617969/document) **:** Entropy measures the amount of information in a random variable; it’s the average length of the message needed to transmit an outcome of that variable using the optimal code.

  - **Information content**: $I(E)=-\log [\Pr(E)]=-\log (P)$

  - Define the **entropy** as the expected value of information: 
    $$
    H(X)=E[I(X)]=E[-\log (P(X))]=-\sum_{i=1}^nP(x_i)\log (P(x_i))
    $$

- [**One-attribute-rule(OneR)**](http://www.saedsayad.com/oner.htm) **:**  The idea of the OneR (one-attribute-rule) algorithm is to find the one attribute to use that makes fewest prediction errors.





### 10. What is Cross Entropy and KL divergence?Describe KL divergence intuitively.

**Cross entropy** is, at its core, a way of measuring the “distance” between two probability distributions P and Q. As you observed, **entropy** on its own is just a measure of a single probability distribution. As such, if we are trying to find a way to model a **true probability distribution, P,** using, say, a neural network to produce an **approximate probability distribution Q**, then there is the need for some sort of distance or difference measure which can be minimized.

**Cross entropy function**:
$$
H(p,q)=H(p)+D_{KL}(p||q)
$$

- The first term, the **entropy** of the true probability distribution *p*, during optimization is ***fixed*** – it reduces to an additive constant during optimization. 
- Only the parameters of the second, approximation distribution, *q* that can be varied during optimization – and hence *the core of the cross entropy measure* of distance is the **KL divergence** function.

**KL divergence** is an expression of “surprise” – under the assumption that *P* and *Q* are *close,* it is *surprising* if it turns out that they are NOT CLOSE, hence in those cases the KL divergence will be high. If they are CLOSE together, then the KL divergence will be low.

**KL divergence** is the i*nformation gained* when we move from a prior distribution *Q* to a posterior distribution *P*. 



**Derivation of KL divergence**:

The expression for KL divergence can also be derived by using a likelihood ratio approach.

**The likelihood ratio** 
$$
LR=\frac{p(x)}{q(x)}
$$

- **Interpretation**: if a value x is sampled from some unknown distribution, the likelihood ratio expresses how much more likely the sample has come from distribution p than from distribution q. If it is more likely from p, the LR > 1, otherwise if it is more likely from q, the LR < 1.

Let’s say we have lots of independent samples and we want to estimate the likelihood function taking into account all this evidence – it then becomes:
$$
LR=\prod_{i=1}^n\frac{p(x_i)}{q(x_i)}
$$
If we convert the ratio to $log$, it’s possible to turn the product in the above definition to a summation:
$$
LR=\sum_{i=1}^n\log \frac{p(x_i)}{q(x_i)}
$$
So now we have the likelihood ratio as a *summation*. Let’s say we want to answer the question of how much, on average, each sample gives evidence of  $p(x)$  over $q(x)$ . To do this, we can take the *expected value* of the likelihood ratio and arrive at:
$$
D_{KL}(P||Q)=\sum_{i=1}^n p(x_i)\log \frac{p(x_i)}{q(x_i)}
$$
The expression above is the definition of KL divergence. It is basically the expected value of the likelihood ratio – where the likelihood ratio expresses how much more likely the sampled data is from distribution P instead of distribution Q. Another way of expressing the above definition is as follows (using log rules):
$$
D_{KL}(P||Q)=\sum_{i=1}^n p(x_i)\log p(x_i)-\sum_{i=1}^n p(x_i)\log q(x_i)
$$

- The first term in the above equation is the ***entropy*** of the distribution P. As you can recall it is the expected value of the ***information content*** of P. 
- The second term  is the information content of Q, but instead weighted by the distribution P. 
- This yields the *interpretation of the KL divergence* to be something like the following: if P is the “true” distribution, then the KL divergence is the amount of **information “lost”** when expressing it via Q.

**Cross entropy**:
$$
\begin{align}
H(p,q)& =H(p)+D_{KL}(p||q) \\
&= -\sum_{i=1}^nP(x_i)\log (P(x_i))+\sum_{i=1}^n p(x_i)\log p(x_i)-\sum_{i=1}^n p(x_i)\log q(x_i) \\
&= -\sum_{i=1}^n p(x_i)\log q(x_i)
\end{align}
$$

> From DL Book

If we have two separate probability distributions $P(x)$ and $Q(x)$ over the same random variable $x$, we can measure how different these two distributions are using the **Kullback-Leibler (KL) divergence**:
$$
D_{KL}(P||Q)=E_{x \sim P}[\log \frac{P(x)}{Q(x)}]=E_{x \sim P}[\log P(x)- \log Q(x)]
$$
The **KL divergence** has many useful properties

- **Nonnegative**. The KL divergence is 0 if and only if P and Q are the same distribution in
  the case of discrete variables, or equal “almost everywhere” in the case of continuous
  variables.
- **Asymmetric**: $D_{KL}(P||Q) \neq D_{KL}(Q||P)$,  this asymmetry means that there are important consequences to the choice of whether to use $D_{KL}(P||Q) $ or $D_{KL}(Q||P)$.

 ***Cross-entropy:*** $H(P, Q) = H(P) +D_{KL}(P||Q) $ 
$$
H(P,Q)=-E_{x \sim P} \log Q(x)
$$
Minimizing the **cross-entropy** with respect to $Q$ is equivalent to minimizing the **KL divergenc**e, because $Q$ does not participate in the omitted term.

**Ref:**

 [machine-learning-interview-questions](https://github.com/Sroy20/machine-learning-interview-questions)

[Common Loss functions in machine learning](https://towardsdatascience.com/common-loss-functions-in-machine-learning-46af0ffc4d23)

[What's the difference between Error, Risk and Loss?](https://datascience.stackexchange.com/questions/35928/whats-the-difference-between-error-risk-and-loss)

[Understanding the Bias-Variance Tradeoff](https://towardsdatascience.com/understanding-the-bias-variance-tradeoff-165e6942b229)

[An introduction to entropy, cross entropy and KL divergence in machine learning](https://adventuresinmachinelearning.com/cross-entropy-kl-divergence/)

