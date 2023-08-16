---
title: "CS229 Note: Linear Regression, Logistic regression, Generalized Linear Models"
abbrlink: 9b3e7e9e
date: 2019/10/20 16:50:20
tags:
- Linear Regression
- Logistic Regression
- Regression
categories: 
- Machine Learning
- CS229
mathjax: true
comments: true
---



**linear function**: $h_θ(x) = θ_0 + θ_1x_1 + θ_2x_2+...+θ_dx_d=θ^Tx$

**cost function**: $J(θ)=\frac{1}{2}\sum_{i=1}^n(h_θ(x^{(i)})-y^{(i)})^2$

<!--more-->

# Linear Regression

## Least Mean Square Algorithm

To minimizes $J(θ)$, consider the ***gradient descent*** algorithm, which starts with some initial $θ$, and repeatedly performs the update:
$$
θ_j:= θ_j − α\frac{∂J(θ)}{∂θ_j}
$$
(This update is simultaneously performed for all values of $j = 0, . . . , d$.)

Gradient descent algorithm repeatedly takes a step in the direction of *steepest decrease* of $J$.



Work out partial derivative term with only one training example (x, y):
$$
\frac{∂J(θ)}{∂θ_j}=\frac{∂}{∂θ_j}\frac{1}{2}\sum_{i=1}^n(h_θ(x)-y)^2 \\
=(h_θ(x)-y)\frac{∂}{∂θ_j}(h_θ(x)-y) \\
=(h_θ(x)-y)\frac{∂}{∂θ_j}(\sum_{i=1}^d θ_ix_i-y) \\
=(h_θ(x)-y)x_j
$$
For a single training example, this gives the update rule - ***LMS update rule***:
$$
θ_j:= θ_j + α(y^{(i)}-h_θ(x^{(i)}))x_j^{(i)}
$$
**Properties**: the magnitude of the update is proportional to the error term $(y^{(i)}-h_θ(x^{(i)}))$ 



**Batch gradient descent**: looks at every example in the entire training set on every step.

- susceptible to local minima

$$
θ:= θ + α\sum_{i=1}^n(y^{(i)}-h_θ(x^{(i)}))x^{(i)}
$$

**Stochastic gradient descent**: repeatedly run through the training set, and each time we encounter a training example, we update the parameters according to the gradient of the error with respect to that single training example only.
$$
θ:= θ + α(y^{(i)}-h_θ(x^{(i)}))x^{(i)}
$$
**Stochastic gradient descent V.S. Batch gradient descent**:

Whereas *batch gradient descent* has to scan through the entire training set before taking a single step—a costly operation if $n$ is large—*stochastic gradient descent* can start making progress right away, and continues to make progress with each example it looks at.





## The normal equations

### Matrix derivatives

For a function $f : \real^{n\times d} → \real$ mapping from $n$-by-$d$ matrices to the real numbers, we define the derivative of $f$ with respect to $A$ to be:
$$
\triangledown _A f(A)=\begin{bmatrix}\frac{\partial f}{\partial A_{11}}& ... & \frac{\partial f}{\partial A_{1d}} \\... & ... & ... \\\frac{\partial f}{\partial A_{n1}} &... & \frac{\partial f}{\partial A_{nd}} \end{bmatrix}
$$

### Least squares revisited

To find in closed-form the value of $θ$ that minimizes $J(θ)$.

Given a training set, define the **design matrix** $X$ to be the $n$-by-$d$ matrix (actually $n$-by-$d + 1$, if we include the intercept term) that contains the training examples’ input values in its rows:
$$
X=\begin{bmatrix}-(x^{(1)})^T- \\-(x^{(2)})^T-\\...\\-(x^{(n)})^T- \end{bmatrix}
$$
Also, let $\overrightarrow{y}$ be the n-dimensional vector containing all the target values from the training set:
$$
\overrightarrow{y}=\begin{bmatrix} y^{(1)} \\y^{(2)}\\...\\y^{(n)} \end{bmatrix}
$$
Since $h_θ(x^{(i)}) = (x^{(i)})^Tθ$, we can easily verify that
$$
Xθ-\overrightarrow{y} = \begin{bmatrix}(x^{(1)})^Tθ \\(x^{(2)})^Tθ\\...\\(x^{(n)})^Tθ\end{bmatrix}-\begin{bmatrix} y^{(1)} \\y^{(2)}\\...\\y^{(n)} \end{bmatrix} \\
 = \begin{bmatrix}h_θ(x^{(1)})-y^{(1)} \\h_θ(x^{(2)})-y^{(2)} \\...\\h_θ(x^{(n)})-y^{(n)} \end{bmatrix}
$$
For a vector $z$, we have that $z^Tz=\sum_i z_i^2$
$$
\frac{1}{2}(Xθ-\overrightarrow{y})^T(Xθ-\overrightarrow{y})=\frac{1}{2}\sum_{i=1}^n (h_θ(x^{(i)})-y^{(i)} )^2 =J(θ)
$$
To minimize $J$, let’s find its derivatives with respect to $θ$.
$$
\begin{align}\triangledown_θ J(θ)&=\triangledown_θ\frac{1}{2}(Xθ-\overrightarrow{y})^T(Xθ-\overrightarrow{y}) \\
&=\frac{1}{2}\triangledown_θ (θ^TX^TXθ-θ^TX^T\overrightarrow{y}-\overrightarrow{y}^TXθ+\overrightarrow{y}^T\overrightarrow{y}) \\
&=\frac{1}{2}\triangledown_θ (θ^T(X^TX)θ-2θ^TX^T\overrightarrow{y}) \\
&=\frac{1}{2}(2(X^TX)θ-2X^T\overrightarrow{y}) \\
&=X^TXθ-X^T\overrightarrow{y}
\end{align}
$$
Note:
$$
a^T b = b^T a \\
\triangledown_x b^Tx=b \\
\triangledown_xx^TAx=2Ax 
$$
for symmetric matrix $A$.

To minimize $J$, we set its derivatives to zero, and obtain the normal equations:
$$
θ=(X^TX)^{-1}X^T\overrightarrow{y}
$$


## Probabilistic interpretation

**Assumptions:**

1. Assume that the target variables and the inputs are related via the equation:

$$
y^{(i)}=θ^Tx^{(i)}+\epsilon^{(i)}
$$

​		where $\epsilon^{(i)}$ is an error term that captures either unmodeled effects or random noise.

2. Assume  $\epsilon^{(i)} \sim \mathcal{N}(0,σ^2) $  ;

3. Assume $\epsilon^{(i)}$ are IID.

Thus, the density of $\epsilon^{(i)}$  is given by:
$$
p(\epsilon^{(i)})=\frac{1}{\sqrt{2\pi}\sigma} \exp(-\frac{(\epsilon^{(i)})^2}{2\sigma^2})
$$
This implies that
$$
p(y^{(i)}|x^{(i)};\theta)=\frac{1}{\sqrt{2\pi}\sigma} \exp(-\frac{(y^{(i)}-θ^Tx^{(i)})^2}{2\sigma^2})
$$
The notation “$p(y^{(i)}|x^{(i)};\theta)$” indicates that this is the distribution of $y^{(i)}$ given $x^{(i)}$ and parameterized by $θ$.

When we wish to explicitly view $p(\overrightarrow{y}|X;\theta)$ as a function of $θ$, we will instead call it the **likelihood** function:
$$
L(\theta)=L(\theta;X,\overrightarrow{y})=p(\overrightarrow{y}|X;\theta)
$$
Note that by the independence assumption on the  $\epsilon^{(i)}$ (and hence also the $y^{(i)}$'s given the $x^{(i)}$’s), this can also be written:
$$
\begin{align}L(\theta)&=\prod_{i=1}^np(y^{(i)}|x^{(i)};\theta) \\
&=\prod_{i=1}^n\frac{1}{\sqrt{2\pi}\sigma} \exp(-\frac{(y^{(i)}-θ^Tx^{(i)})^2}{2\sigma^2})
\end{align}
$$
<u>What is a reasonable way of choosing our best guess of the parameters $θ$?</u>

The principal of **maximum likelihood** says that we should choose $θ$ so as to make the data as high probability as possible. I.e., we should choose $θ$ to maximize $L(θ)$, or **log likelihood** $ℓ(θ)$ :
$$
\begin{align}ℓ(θ)&=\log{L(\theta)} \\
&=\log{\prod_{i=1}^n\frac{1}{\sqrt{2\pi}\sigma} \exp(-\frac{(y^{(i)}-θ^Tx^{(i)})^2}{2\sigma^2})} \\
&=\sum_{i=1}^n \log{\frac{1}{\sqrt{2\pi}\sigma} \exp(-\frac{(y^{(i)}-θ^Tx^{(i)})^2}{2\sigma^2})} \\
&=n\log{\frac{1}{\sqrt{2\pi}}\sigma}-\frac{1}{2\sigma^2}\sum_{i=1}^n(y^{(i)}-θ^Tx^{(i)})^2
\end{align}
$$
Hence, maximizing $ℓ(θ)$ gives the same answer as minimizing:
$$
\frac{1}{2}\sum_{i=1}^n(y^{(i)}-θ^Tx^{(i)})^2
$$
which we recognize to be $J(θ)$, our original least-squares cost function.



**To summarize:** Under the previous probabilistic assumptions on the data, *least-squares regression* corresponds to finding the *maximum likelihood* estimate of $θ$. This is thus one set of assumptions under which least-squares regression can be justified as a very natural method that’s just doing maximum likelihood estimation.

Note also that, our final choice of $θ$ did not depend on what was $σ^2$.



## Locally weighted linear regression

**Locally weighted linear regression (LWR)** algorithm assumes there is sufficient training data, makes the choice of features less critical.

In the *original linear regression algorithm*, to make a prediction at a query point x (i.e., to evaluate h(x)), we would:

1. Fit $θ$ to minimize $\sum_{i=1}^n(y^{(i)}-θ^Tx^{(i)})^2$.
2. Output $θ^T x$.

In the *locally weighted linear regression* algorithm:

1. Fit $θ$ to minimize $\sum_{i=1}^nw^{(i)}(y^{(i)}-θ^Tx^{(i)})^2$.
2. Output $θ^T x$.

A fairly standard choice for the weights $w^{(i)}$ is:
$$
w^{(i)}=\exp{-\frac{(x^{(i)}-x)^2}{2\tau^2}}
$$
Note that the weights depend on the particular point x at which we’re trying to evaluate x. Moreover, if $|x^{(i)} − x|$ is small, then $w^{(i)}$ is close to 1; and if $|x^{(i)} − x|$ is large, then $w^{(i)}$ is small. Hence, $θ$ is chosen giving a much higher “weight” to the (errors on) training examples close to the query point $x$.

$τ$ is called the **bandwidth** parameter, which controls how quickly the weight of a training example falls off with distance of its $x^{(i)}$ from the query point $x$.



### **Non-parametric** V.S. Parametric

**Non-parametric algorithm**: Locally weighted linear regression

- The amount of stuff we need to keep in order to represent the hypothesis $h$ grows linearly with the size of the training set.

**Parametric algorithm**: linear regression algorithm

- A fixed, finite number of parameters (the $θ_i$’s)







# Logistic regression

Change the form for our **hypotheses $h(x)$:**
$$
h_\theta(x)=g(\theta^Tx)=\frac{1}{1+\exp{(-\theta^Tx)}}
$$
**Logistic function(sigmoid function):**
$$
g(z)=\frac{1}{1+\exp{(-z)}}
$$


<img src="./sigmoid.png" width="600" />


$$
\begin{align}g^{'}(z)&=\frac{d}{dz}\frac{1}{1+\exp{(-z)}} \\
&=\frac{1}{(1+\exp{(-z)})^2} \exp{(-z)} \\
&=\frac{1}{1+\exp{(-z)}} \cdot (1-\frac{1}{1+\exp{(-z)}}) \\
&=g(z) \cdot (1-g(z))
\end{align}
$$


## How do we fit $θ$ for it?**

**Probabilistic assumptions**:
$$
P(y=1|x;\theta)=h_\theta(x) \\
P(y=0|x;\theta)=1-h_\theta(x)
$$
Note that this can be written more compactly as
$$
p(y|x;\theta)=(h_\theta(x) )^y(1-h_\theta(x) )^{1-y}
$$
Assuming that the $n$ training examples were generated independently, we can then write down the likelihood of the parameters as
$$
L(\theta)=p(\overrightarrow{y}|X;\theta)=\prod_{i=1}^np(y^{(i)}|x^{(i)};\theta) \\
=\prod_{i=1}^n(h_\theta(x^{(i)}) )^{y^{(i)}}(1-h_\theta(x^{(i)}) )^{1-y^{(i)}}
$$
As before, it will be easier to maximize the log likelihood:
$$
ℓ(θ) = \log{L(θ)}=\sum_{i=1}^ny^{(i)}\log{h_\theta(x^{(i)})}+(1-y^{(i)})\log{(1-h_\theta(x^{(i)}) )}
$$
Use gradient ascent to maximize the likelihood: $θ := θ + α∇ℓ(θ)$

Let’s start by working with just one training example $(x, y)$:
$$
\begin{align}
\frac{\partial ℓ(θ)}{\partial θ_j}&=(y\frac{1}{h_\theta(x)}-(1-y)\frac{1}{1-h_\theta(x)})\frac{\partial h_\theta(x)}{\partial θ_j} \\
&=(y\frac{1}{g(\theta^Tx)}-(1-y)\frac{1}{1-g(\theta^Tx)})\frac{\partial g(\theta^Tx)}{\partial θ_j} \\
&=(y\frac{1}{g(\theta^Tx)}-(1-y)\frac{1}{1-g(\theta^Tx)}) g(\theta^Tx)(1- g(\theta^Tx))\frac{\partial θ^Tx}{\partial θ_j} \\
&=(y(1- g(\theta^Tx))-(1-y)g(\theta^Tx))x_j \\
&=(y-g(\theta^Tx))x_j \\
&=(y-h_\theta(x))x_j
\end{align}
$$
**Stochastic gradient ascent rule**:
$$
θ_j := θ_j + α(y^{(i)}-h_\theta(x^{(i)}))x^{(i)}_j
$$
It looks identical to the LMS update rule; but this is not the same algorithm, because $h_\theta(x^{(i)})$ is now defined as a non-linear function of $\theta^Tx^{(i)}$.



## Digression: The perceptron learning algorithm

Consider modifying the logistic regression method to “force” it to output values that are either 0 or 1 or exactly：
$$
g(z)=\begin{cases}1 & z \geq 0\\0 & x < 0\end{cases}
$$
Let $h(x) = g(θ^T x)$ as before but using this modified definition of $g$, and if we use the update rule
$$
θ_j := θ_j + α(y^{(i)}-h_\theta(x^{(i)}))x^{(i)}_j
$$
then we have the **perceptron learning algorithn**.

Note:

- Even though the perceptron may be cosmetically similar to the other algorithms we talked about, it is actually a very different type of algorithm than logistic regression and least squares linear regression;
- Difficult to endow the perceptron’s predictions with meaningful probabilistic interpretations, or derive the perceptron as a maximum likelihood estimation algorithm.



## Fisher scoring algorithm for maximizing ℓ(θ)

**Newton’s method** (finding a zero of a function) performs the following update:
$$
\theta:=\theta-\frac{f(\theta)}{f^{'}(\theta)}
$$
**Interpretation:** Approximating the function $f$ via a linear function that is tangent to $f$ at the current guess $θ$, solving for where that linear function equals to zero, and letting the next guess for $θ$ be where that linear function is zero.

<img src="./newton.png" width="600" />

The maxima of $ℓ$ correspond to points where its first derivative $ℓ′(θ)$ is zero. So, by letting $f(θ) = ℓ′(θ)$, we can use the same algorithm to maximize $ℓ$, and we obtain update rule:
$$
\theta:=\theta-\frac{ℓ′(θ)}{ℓ′′(θ)}
$$
<u>The generalization of Newton’s method:</u>
$$
\theta:=\theta-H^{-1}∇_θℓ(θ)
$$
$H$ is **Hessian** matrix, whose entries are given by $H_{ij}=\frac{\partial^2 ℓ(θ)}{\partial θ_i \partial θ_j}$

- Faster convergence than (batch) gradient descent, and requires many fewer iterations to get very close to the minimum.
- One iteration of Newton’s can, however, be more expensive than one iteration of gradient descent, since it requires finding and inverting an d-by-d Hessian

When ***Newton’s method*** is applied to maximize the ***logistic regression log likelihood function ℓ(θ)***, the resulting method is also called **Fisher scoring**.







# Generalized Linear Models

## The exponential family

We say that a class of distributions is in the exponential family if it can be written in the form
$$
p(y;η) = b(y) \exp{(η^TT(y) − a(η))}
$$

- $η$ : **natural parameter**/ **canonical parameter** of the distribution
- $T(y)$:  **sufficient statistic** （often be the case that $T(y) = y$）
- $a(η)$: **log partition function**
- $e^{−a(η)}$: plays the role of a normalization constant, that makes sure the distribution $p(y; η)$ sums/integrates over $y$ to 1.

A fixed choice of $T, a$ and $b$ defines a family (or set) of distributions that is parameterized by $η$; as we vary $η$, we then get different distributions within this family.

### Bernoulli($\phi$)

Bernoulli distribution:
$$
p(y = 1; \phi) = \phi; p(y = 0; \phi) = 1 − \phi.
$$
We write the Bernoulli distribution as:
$$
\begin{align}p(y;\phi)&=\phi^y(1-\phi)^{1-y} \\
&=\exp{(y\log{\phi})+(1-y)\log{(1-\phi)}}  \\
&=\exp((\log{(\frac{\phi}{1-\phi}}))y+\log{(1-\phi)})
\end{align}
$$

-  **natural parameter $η$**: $\log{(\frac{\phi}{1-\phi}})$
- **sufficient statistic $T(y)$**: $y$
- **log partition function $a(η)$**: $-\log{(1-\phi)}=\log{1+e^{η}}$



### Gaussian distribution $\mathcal{N}(\mu,\sigma^2)$

Recall that, when deriving linear regression, the value of $σ^2$ had no effect on our final choice of $θ$ and $h(x)$. To simplify the derivation below, let’s set $σ^2 = 1.$ We then have:
$$
p(y;\mu)=\frac{1}{\sqrt{2\pi}}\exp{(-\frac{1}{2}(y-\mu)^2)} \\
=\frac{1}{\sqrt{2\pi}}\exp{(-\frac{1}{2}y^2)}\exp{(y\mu-\frac{1}{2}\mu^2)}
$$
Thus Gaussian is in the exponential family, with:

- $η = μ$
- $T=1$
- $b(y)=\frac{1}{\sqrt{2\pi}}\exp{(-\frac{1}{2}y^2)}$
- $a(η)=\frac{1}{2}\mu^2=\frac{1}{2}η^2$
- $T(y)=y$



### Multinomial distribution

For *n* independent trials each of which leads to a success for exactly one of *k* categories, with each category having a given fixed success probability, the **multinomial distribution** gives the probability of any particular combination of numbers of successes for the various categories.

When *k* is 2 and *n* is 1, the multinomial distribution is the **Bernoulli distribution**. When *k* is 2 and *n* is bigger than 1, it is the **binomial distribution**. When k is bigger than 2 and *n* is 1, it is the **Categorical distribution**.

Mathematically, we have *k* possible mutually exclusive outcomes, with corresponding probabilities $\phi_1, ..., \phi_k$*, and *n* independent trials. Since the *k* outcomes are mutually exclusive and one must occur we have  $\phi_i \geq 0$ for $   i = 1, ..., k$ and $\sum_{i=1}^k \phi_i = 1$.

To parameterize a **multinomial** over $k$ possible outcomes, one could use $k$ parameters $\phi_1, . . . , \phi_{k-1}$, as they must satisfy $\sum_{i=1}^k \phi_i = 1)$, we can let $\phi_{k}=1-\sum_{i=1}^{k-1} \phi_i$. 

To express the multinomial as an **exponential family distribution**, we will define $T(y) ∈ R^{k−1}$ as follows:
$$
T(1)=\begin{bmatrix}1\\0\\0\\...\\0\end{bmatrix} , T(2)=\begin{bmatrix}0\\1\\0\\...\\0\end{bmatrix} , T(3)=\begin{bmatrix}0\\0\\1\\...\\0\end{bmatrix} ,T(k-1)=\begin{bmatrix}0\\0\\0\\...\\1\end{bmatrix} ,T(k)=\begin{bmatrix}0\\0\\0\\...\\0\end{bmatrix}
$$
Unlike our previous examples, here we do not have $T(y) = y$; also, $T(y)$ is now a $k − 1$ dimensional vector.

We will write $(T(y))_i$ to denote the i-th element of the vector $T(y)$. We can also write the relationship between $T(y)$ and $y$ as $(T(y))_i = 1\{y = i\}$. Further, we have that $E[(T(y))_i] = P(y = i) = \phi_i$.
$$
\begin{align}
p(y; \phi) &=  \phi_1^{1\{y=1\}} \phi_2^{1\{y=2\}} ... \phi_k^{1\{y=k\}} \\
&=  \phi_1^{1\{y=1\}} \phi_2^{1\{y=2\}} ... \phi_k^{1-\sum_{i=1}^{k-1}1\{y=i\}} \\
&= \phi_1^{(T(y))_1} \phi_2^{(T(y))_2} ... \phi_k^{1-\sum_{i=1}^{k-1}(T(y))_i} \\
&= \exp{\left[(T(y))_1 \log{\phi_1}+(T(y))_2 \log{\phi_2}+...+(1-\sum_{i=1}^{k-1}(T(y))_i) \log{\phi_k}\right]} \\
&=\exp{\left[(T(y))_1 \log{\frac{\phi_1}{\phi_k}}+ (T(y))_2 \log{\frac{\phi_2}{\phi_k}} +...+(T(y))_{k-1} \log{\frac{\phi_{k-1}}{\phi_k}}+\log{\phi_k} \right]} \\
&=b(y) \exp{(η^TT(y) − a(η))}
\end{align}
$$
where
$$
b(y)=1 \\
a(η)=-\log{\phi_k} \\
η=\begin{bmatrix}\log{\frac{\phi_1}{\phi_k}} \\ ... \\ \log{\frac{\phi_{k-1}}{\phi_k}} \\\log{\frac{\phi_1}{\phi_k}}\end{bmatrix}
$$
To invert the link function and derive the response function, we therefore have that
$$
e^{η_i}=\frac{\phi_i}{\phi_k}  \\
\phi_ke^{η_i}=\phi_i \\
\phi_k \sum_{i=1}^ke^{η_i}=\sum_{i=1}^k\phi_i=1 \\
$$
This implies that
$$
\phi_k =\frac{1}{\sum_{i=1}^ke^{η_i}}
$$
Which means:
$$
\phi_i =\frac{e^{η_i}}{\sum_{i=1}^ke^{η_i}}
$$
This function mapping from the $η$’s to the $\phi$ ’s is called the **softmax function**.









### Other Members of the Exponential Family

- Multinomial 
- Poisson 
- Gamma and Exponential (for modelling continuous, non-negative random variables, such as time intervals); 
- Beta and the Dirichlet (for distributions over probabilities);



## Constructing GLMs

Consider a classification or regression problem where we would like to predict the value of some random variable $y$ as a function of $x$. To derive a GLM for this problem, we will make the following three assumptions about the conditional distribution of $y$ given $x$ and about our model:

1. $y | x; θ ∼ ExponentialFamily(η)$. I.e., given $x$ and $θ$, the distribution of $y$ follows some exponential family distribution, with parameter $η$.
2. Given $x$, our goal is to predict the expected value of $T(y)$ given $x$. In most of our examples, we will have $T(y) = y$, so this means $w$ would like the prediction $h(x)$ output by our learned hypothesis $h$ to satisfy $h(x) = E[y|x]$. For instance, in logistic regression, we had $h(x) = p(y = 1|x; θ) = 0 \cdot p(y = 0|x; θ) + 1 \cdot p(y = 1|x; θ) = E[y|x; θ].)$ 
3. The natural parameter $η$ and the inputs x are related linearly: $η = θ^T x$. (Or, if $η$ is vector-valued, then $η_i = θ^T_i x$.)



### Ordinary Least Squares

<u>Ordinary least squares is a special case of the GLM family of models</u>

We let the $ExponentialFamily(η)$ distribution above be the **Gaussian distribution**. In the formulation of the Gaussian as an exponential family distribution, we had $μ = η$.
$$
h_\theta(x)=E[y|x;\theta] \\
=\mu \\
=η \\
=\theta^Tx
$$
The first equality follows from *Assumption 2*, above; the second equality follows from the fact that $y|x; θ ∼ \mathcal{N}(μ, σ^2)$, and so its expected value is given by $μ$; the third equality follows from *Assumption 1* (and our earlier derivation showing that μ = η in the formulation of the Gaussian as an exponential family distribution); and the last equality follows from Assumption 3.



### Logistic Regression

Given that $y$ is binary-valued, it therefore seems natural to choose the **Bernoulli** family of distributions to model the conditional distribution of $y$ given $x$.

- If $y|x; θ ∼ Bernoulli(φ)$, then $E[y|x; θ] = φ$.
- In our formulation of the Bernoulli distribution as an exponential family distribution, we had $φ = 1/(1 + e^{−η})$

$$
h_\theta(x)=E[y|x;\theta] \\
=φ \\
=1/(1 + e^{−η}) \\
=1/(1 + e^{−\theta^Tx})
$$

**canonical response function**: the function $g$ giving the distribution’s mean as a function of the natural parameter $g(η) = E[T(y); η]$

**canonical link function**: g's inverse $g^{-1}$

Thus, the <u>canonical response function</u> for the Gaussian family is just the **identify function**; and the <u>canonical response function</u> for the **Bernoulli** is the **logistic function**



### Softmax Regression

Consider a classification problem in which the response variable $y$ can take on any one of $k$ values, so $y ∈\{1, 2, . . . , k\}$.

We will thus model it as distributed according to a multinomial distribution.

Remember the **softmax function**:
$$
\phi_i =\frac{e^{η_i}}{\sum_{i=1}^ke^{η_i}}
$$
Assumption 3 $η_i$’s are linearly related to the $x$’s: $η_i=\theta^Tx_i$, (for $i = 1, . . . , k − 1$)

For notational convenience, we can also define $\theta_k=0$, so that $η_k = θ^T_k x = 0$

Hence, our model assumes that the conditional distribution of $y$ given $x$ is given by
$$
p(y = i|x; θ) = \phi_i \\
=\frac{e^{η_i}}{\sum_{j=1}^ke^{η_j}} \\
=\frac{e^{θ^T_i x}}{\sum_{j=1}^ke^{θ^T_j x}}
$$
This model, which applies to classification problems where $y ∈ \{1, . . . , k\}$, is called **softmax regression**.It is a generalization of logistic regression.

Our hypothesis will output
$$
h_\theta(x) = E[T(y)|x; θ]\\
=\begin{bmatrix}1\{y=1\} \\1\{y=2\} \\...\\1\{y=k\}  \end{bmatrix}|x;\theta \\
=\begin{bmatrix}\phi_1 \\\phi_2 \\...\\\phi_{k-1} \end{bmatrix}  \\
=\begin{bmatrix}\frac{e^{θ^T_1 x}}{\sum_{j=1}^ke^{θ^T_j x}}\\ \frac{e^{θ^T_2 x}}{\sum_{j=1}^ke^{θ^T_j x}} \\...\\\frac{e^{θ^T_{k-1} x}}{\sum_{j=1}^ke^{θ^T_j x}}\end{bmatrix}
$$
**Parameter fitting:**

Training data:${(x^{(i)}, y^{(i)}); i = 1, . . . , n}$ . Log-likelihood:
$$
ℓ(θ) = \log{L(θ)} \\
=\sum_{i=1}^n \log{p(y^{(i)}|x^{(i)};\theta)} \\
=\sum_{i=1}^n \log{\prod_{l=1}^k(\frac{e^{θ^T_l x^{(i)}}}{\sum_{j=1}^ke^{θ^T_j x^{(i)}}})^{1\{y^{(i)}=l\}}}
$$
Maximize $ℓ(θ)$ in terms of $θ$, using a method such as **gradient ascent** or **Newton’s method**

