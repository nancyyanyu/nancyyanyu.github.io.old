---
title: "Probability & Statistics Fundamental"
date: 2020/07/15 15:14:40
categories: Math
mathjax: true
comments: true
abbrlink: c27004a0
tags:
- Statistics
- Probability
- ANOVA
- Hypothesis Testing
---



Basic Probability & Statistics knowledge.

<!--more-->



# Random Variables

**Bernoulli**

- **Story:** A trial is performed with probability $p$ of 'succes', and $X$ is the indicator of success: $1$ means success, $0$ means failure.
- **PMF:** $\begin{align}p(X=1) &= p \\p(X=0) &= 1 − p \end{align}$
- **Expectation:** $p$
- **Variance:** $p(1-p)$



**Binomial**

- **Story:** $X$ is the number of 'successes' that we will achieve in $n$ independent trials, where each trial is either a success or a failure, each with the same probability $p$ of success.
- **PMF:** $p(k)=\binom{n}{k} p^k(1-p)^{n-k}$
- **Expectation:**  $np$
- **Variance:**  $np(1-p)$

- **Property:** Let $X \sim \mathcal{Bin}(n,p), Y \sim \mathcal{Bin}(m,p)$ with $X  \text{ independent with } Y$.
  - **Redefine success** : $n-X \sim \mathcal{Bin}(n,1-p)$
  - **Sum**: $X+Y \sim \mathcal{Bin}(n+m,p)$
  - **Binomial-Poisson Relationship**: $\mathcal{Bin}(n, p)$ is approximately  $\mathcal{Pois}(\lambda=np)$ if $p$ is small and $n$  is large.
  - **Binomial-Normal Relationship**: $\mathcal{Bin}(n, p)$ is approximately $\mathcal{N}(np,np(1-p))$ if $n$ is large and $p$ is not near $0$ or $1$.
  - **Binomial-Bernoulli Relationship**:let $X_1, X_2, . . . , X_n$ be *independent* **Bernoulli random** variables with $p(X_i = 1) = p$. Then $Y = X_1 + X_2 + ··· + X_n$ is a **Binomial random variable**.



**Geometric**

- **Story:**  $X$ is the number of ``failures" that we will achieve before we achieve our first success. Our successes have probability $p$.
- **PMF:** $p(k)=P(X=k)=(1-p)^{k}p, \quad k=1,2,3$
- **Expectation:** $\frac{1-p}{p}$
- **Variance:** $\frac{1-p}{p^2}$



**Negative Binomial Distributions**

- **Story:**$X$ is the number of "failures" that we will have before we achieve our $r$th success. Our successes have probability $p$.
- **PMF:** $p(X=k)=\left(\begin{array}{c}r+k-1\\ r-1\end{array}\right) p^r(1-p)^{k}$
- **Expectation:** $\frac{r(1-p)}{p}$
- **Variance:** $\frac{r(1-p)}{p^2}$



**Poisson Distribution**

- **Story: ** There are rare events (low probability events) that occur many different ways (high possibilities of occurences) at an average rate of **$\lambda$ occurrences per unit space or time**. The number of events that occur in that unit of space or time is $X$.
- **Example**: A certain busy intersection has an average of 2 accidents per month. Since an accident is a low probability event that can happen many different ways, it is reasonable to model the **number of accidents in a month at that intersection** as $\mathcal{Pois}(2)$. Then the number of accidents that happen in two months at that intersection is distributed $\mathcal{Pois}(4)$
- **PMF:** $p(X=k)=\frac{e^{-\lambda}\lambda^k }{k!}, \quad k=0,1,2,3$ 
- **Expectation:** $\lambda$
- **Variance:** $\lambda$
- **Property:** Let $X \sim \mathcal{Pois}(\lambda_1)$ and $Y \sim \mathcal{Pois}(\lambda_2)$, with $X \perp \!\!\! \perp  Y$.
  - **Sum**: $X + Y \sim \mathcal{Pois}(\lambda_1 + \lambda_2)$
  - **Conditional**: $X | (X + Y = n) \sim \mathcal{Bin}\left(n, \frac{\lambda_1}{\lambda_1 + \lambda_2}\right)$
  - **Chicken-egg**: If there are $Z \sim \mathcal{Pois}(\lambda)$ items and we randomly and independently "accept" each item with probability $p$, then the number of accepted items $Z_1 \sim \mathcal{Pois}(\lambda p)$, and the number of rejected items $Z_2 \sim \mathcal{Pois}(\lambda (1-p))$, and $Z_1  \perp \!\!\! \perp   Z_2$



**Uniform Distribution**

- **Story: **A **uniform random variable** on the interval $[a, b]$ is a model for what we mean when we say “choose a number at random between $a$ and $b$.
- **PMF:** $f(x)=\frac{1}{b-a}, x\in (a,b)$ 
- **Expectation:** $\frac{a+b}{2}$
- **Variance:** $\frac{(b-a)^2}{12}$
- **Property:** For a Uniform distribution, the probability of a draw from any interval within the support is proportional to the length of the interval



**Normal Distribution** $\mathcal{N}(\mu,\sigma^2)$

- **Central Limit Theorem**: the sample mean of i.i.d.~r.v.s will approach a Normal distribution as the sample size grows, regardless of the initial distribution.
- **Location-Scale Transformation**: Every time we shift a Normal r.v.~(by adding a constant) or rescale a Normal (by multiplying by a constant), we change it to another Normal r.v. For any Normal $X \sim \mathcal{N}(\mu, \sigma^2)$, we can transform it to the standard $\mathcal{N}(0, 1)$ by: $Z= \frac{X - \mu}{\sigma} \sim \mathcal{N}(0, 1)$ 
- **Standard Normal**: The Standard Normal, $Z \sim \mathcal{N}(0, 1)$, has mean $0$ and variance $1$. Its CDF is denoted by $\Phi$.
- **PMF:** $f(x)=\frac{1}{\sigma \sqrt{2 \pi} }e^{-\frac{(x-\mu)^2}{2\sigma^2}}, x\in (-\infty,\infty)$ 
- **Expectation:** $\mu$
- **Variance:** $\sigma^2$



**Exponential Distribution** 

- **Story: ** the waiting times between rare events. $\lambda$ is the rate parameter, the next event arrives at a rate of 1 per $1/\lambda$ (hour/minute) on average. The expected time until the next event is $1/\lambda$ 

- **PMF:** $f(x)=\lambda e^{-\lambda x}, x\in (0,\infty)$ 

- **Expectation:** $\frac{1}{\lambda}$ 

- **Variance:** $\frac{1}{\lambda^2}$

- **Property:** 

  - **Expos as a rescaled Expo(1)**: $Y \sim \mathcal{Expo}(\lambda) \rightarrow X = \lambda Y \sim \mathcal{Expo}(1)$

  - **Memorylessness**: The Exponential Distribution is the *only* **continuous memoryless** distribution. The memoryless property says that for $X \sim \mathcal{Expo}(\lambda)$ and any positive numbers $s$ and $t$, 

    $$P(X > s + t | X > s) = P(X > t)$$

    Equivalently,
    $$
    X - a | (X > a) \sim \mathcal{Expo}(\lambda)
    $$

  - **Min of Expos**: If we have independent $X_i \sim  \mathcal{Expo}(\lambda_i)$, then $\min(X_1, \dots, X_k) \sim  \mathcal{Expo}(\lambda_1 + \lambda_2 + \dots + \lambda_k)$

  - **Max of Expos**: If we have i.i.d.~$X_i \sim \mathcal{Expo}(\lambda)$, then $\max(X_1, \dots, X_k)$ has the same distribution as $Y_1+Y_2+\dots+Y_k$, where $Y_j \sim \mathcal{Expo}(j\lambda)$ and the $Y_j$ are independent

  

**Chi-Square** $\chi^2_n$

- **Story: **A Chi-Square($n$) is the **sum of the squares** of $n$ independent **standard Normal** r.v.s.
  $$
  \mathcal{X} \textrm{ is distributed as } Z_1^2 + Z_2^2 + \dots + Z_n^2 \textrm{ for i.i.d.~$Z_i \sim \mathcal{N}(0,1)$} \\
  \mathcal{X} \sim \text{Gamma}(n/2,1/2)
  $$
  

  

**Student-t** $t_n$

- **Story: ** Let $X_1,...X_n$ be i.i.id Normal r.v.s $\sim \mathcal{N}(\mu, \sigma^2)$

$$
\textrm{sample mean: } \bar{X}=\frac{1}{n}\sum_{i=1}^nX_i \\
\textrm{sample variance: }S^2=\frac{1}{n-1}\sum_{i=1}^n(X_i-\bar{X})^2 \\
\frac{\bar{X}-\mu}{S/\sqrt{n}} \sim t(n-1)
$$



# LLN & CLT

**Law of Large Numbers (LLN)**

Let $X_1, X_2, X_3 \dots$ be i.i.d.~with mean $\mu$. The $\textbf{sample mean}$ is 	
$$
\bar{X}_n = \frac{X_1 + X_2 + X_3 + \dots + X_n}{n}
$$


The $\textbf{Law of Large Numbers}$ states that as $n \to \infty$, $\bar{X}_n \to \mu$ with probability $1$. 

For example, in flips of a coin with probability $p$ of Heads, let $X_j$ be the indicator of the $j$th flip being Heads.  Then LLN says the proportion of Heads converges to $p$ (with probability $1$).



**Central Limit Theorem (CLT)**

**Explanation**: the sample mean of i.i.d.~r.v.s will approach a Normal distribution as the sample size grows, regardless of the initial distribution.

We can use the **Central Limit Theorem** to approximate the distribution of a random variable $Y=X_1+X_2+\dots+X_n$ that is a sum of $n$ i.i.d. random variables $X_i$. Let  $E(Y) = \mu_Y$ and $Var(Y) = \sigma^2_Y$. The CLT says
$$
Y \dot{\,\sim\,} \mathcal{N}(\mu_Y, \sigma^2_Y)
$$
If the $X_i$ are i.i.d.~with mean $\mu_X$ and variance $\sigma^2_X$, then $\mu_Y = n \mu_X$ and $\sigma^2_Y = n \sigma^2_X$. For the sample mean $\bar{X}_n$, the CLT says
$$
\bar{X}_n = \frac{1}{n}(X_1 + X_2 + \dots + X_n) \dot{\,\sim\,} \mathcal{N}(\mu_X, \sigma^2_X/n)
$$


**Asymptotic Distributions using CLT**

We use $\xrightarrow{D}$ to denote *converges in distribution to* as $n  \to \infty$. The CLT says that if we standardize the sum $X_1 + \dots + X_n$  then the distribution of the sum converges to $\mathcal{N}(0,1)$ as $n \to \infty$:
$$
\frac{1}{\sigma\sqrt{n}} (X_1 + \dots + X_n - n\mu_X) \xrightarrow{D} \mathcal{N}(0, 1)
$$
In other words, the CDF of the left-hand side goes to the standard Normal CDF, $\Phi$. In terms of the sample mean, the CLT says
$$
\frac{(\bar{X}_n - \mu_X)}{\sigma_X/\sqrt{n} } \xrightarrow{D} \mathcal{N}(0, 1)
$$
**Assumptions of CLT**

- **Randomization Condition:** The data must be sampled randomly. 

- **Independence Assumption**: The sample values must be independent of each other. This means that the occurrence of one event has no influence on the next event. Usually, if we know that people or items were selected randomly we can assume that the independence assumption is met.

- **10% Condition:** When the sample is drawn without replacement (usually the case), the sample size, *n*, should be no more than 10% of the population.

- **Sample Size Assumption:** The sample size must be sufficiently large. Although the Central Limit Theorem tells us that we can use a Normal model to think about the behavior of sample means when the sample size is large enough, it does not tell us how large that should be. If the population is very skewed, you will need a pretty large sample size to use the CLT, however if the population is unimodal and symmetric, even small samples are acceptable. So think about your sample size in terms of what you know about the population and decide whether the sample is large enough. In general a sample size of 30 is considered sufficient if the sample is unimodal (and meets the 10% condition).





#  The Mean, Variance and Standard Deviation

**Population:** whatever unit it is you are measuring something.

**Population parameters:** the parameters that determine how a distribution fits the population data

- **mean** and **standard deviation** of the normal curve, which represents the population.

We rarely, if ever, have population data, so we always *estimate* the <u>population parameters</u> using a relatively small sample.

> The reason why we want to know the <u>population parameters</u> is to ensure that the results drawn from our experiment are ***reproducible***.

The more data we have, the more *confidence* we can have in the accuracy of the estimates.

- **P-values & confidence intervals** : quantify the confidence in the esitmated parameters - > tell us that while the estimates are different, they are not *significantly* different.

By estimating the population parameters and quantifying our confidence in them, we can generate results that are reprducible in future experiments.

$\bar{x}$ : **estimated mean** or **sample mean**.

$\mu$: **population mean**

The estimated mean $\bar{x}$ is different from the population mean $\mu$, but with more and more data, $\bar{x}$ should get closer and closer.

**Population Variance** = $\frac{\sum{(x-\mu)^2}}{n}$ $\leftarrow$ this is the formula we use to *calculate*, not *estimate*, the population variance.

**Population Standard Deviation**=$\sqrt{\frac{\sum{(x-\mu)^2}}{n}}$ 

Note: we almost never calcualte the population mean, population variance and standard deviation.

**Estimated Population Variance (sample variance)** $s^2= \frac{\sum{(x-\bar{x})^2}}{n-1}$

- Dividing by ***n-1*** compensates for the fact that we are calcualting differences from the **sample mean** instead of the **population mean**, otherwise we would consistently underestimate the variance around the population mean.
- This is because the *differences between the data and the sample mean* tend to be smaller than the *differences between the data and the population mean*. Thus the differences around the population mean result in a larger average.

​        $$\frac{\sum{(x-\bar{x})^2}}{n-1} < \frac{\sum{(x-\mu)^2}}{n}$$

**Chi-Squared relation with sample variance**: $\frac{s^2(n-1)}{\sigma^2} \sim \mathcal{X}^2_{n-1}$

**Estimated Population Mean** = $\frac{\sum_i x_i}{n}$

## SD v.s. SE

> **The standard deviation of the means is called The Standard Error**

The **Standard Deviation** quantifies the variation within a set of data points.

The **Stardard Error** quantifies the variation in the *means* from ***multiple sets*** of data.

- The confusing thing is that the SE can be estimated from a single set of data. In almost all cases, **you should plot he SD**, since graphs are usually intended to describe the data that you measured.





# Hypothesis Testing 

**Null Hypothesis**: there is ***no difference*** between things $\rightarrow$ $H_0$

**Alternative Hypothesis**: there is a ***difference*** between things$\rightarrow$ $H_1$

Outcome: A decision about whether or not to ***reject*** or ***fail to reject*** the Null Hypothesis

|                | Accept $H_0$  | Reject $H_0$ |
| -------------- | ------------- | ------------ |
| $H_0$ is true  | ☑             | type I error |
| $H_0$ is false | type II error | ☑            |

**type I error:** the probability of rejecting the null hypothesis while the null hypothesis is true, often noted $\alpha$ and also called "false alarm" or significance level . If we note *T* the **test statistic** and *R* the **rejection region**, then we have:
$$
\alpha=P(T \in R| H_0 \text{ is true})
$$
**type II error:** the probability of not rejecting the null hypothesis while the null hypothesis is not true, often noted $\beta$ and also called "missed alarm" or **"false positive**". 
$$
\beta=P(T\notin R|H_0{\small\textrm{ not true})}
$$
![](type-error.png)

**p-value**: the probability under the **null hypothesis** of having a test statistic *T* at least as extreme as the one that we observed $T_0$. We have:
$$
{\small\textrm{(left-sided)}}\quad\boxed{p\textrm{-value}=P(T\leqslant T_0|H_0{\small\textrm{ true})}}\quad\quad\quad{\small\textrm{(right-sided)}} \quad \boxed{p\textrm{-value}=P(T\geqslant T_0|H_0{\small\textrm{ true})}}
$$

$$
{\small\textrm{(two-sided)}}\quad\boxed{p\textrm{-value}=P(|T|\geqslant |T_0||H_0{\small\textrm{ true})}}
$$

*Remark: the example below illustrates the case of a right-sided *p*-value.

![](p-value.png)

**Testing for the difference in two means**:

The table below sums up the **test statistic** to compute when performing a hypothesis test where the null hypothesis is:
$$
H_0\quad:\quad\mu_X-\mu_Y=\delta
$$

| Distribution of $X_i, Y_i$ | **Sample size $n_X, n_Y$** | **Variance $\sigma_X^2, \sigma_Y^2$** | **Test statistic under $H_0$**                               |
| -------------------------- | -------------------------- | ------------------------------------- | ------------------------------------------------------------ |
| Normal                     | Any                        | Known                                 | $\frac{(\bar{X}-\bar{Y})-\delta}{\sqrt{\frac{\sigma_X^2}{n_X}+\frac{\sigma_Y^2}{n_Y}}} \sim \mathcal{N}(0,1)$ |
| Normal                     | Large                      | Unknown                               | $\frac{(\bar{X}-\bar{Y})-\delta}{\sqrt{\frac{S_X^2}{n_X}+\frac{S_Y^2}{n_Y}}} \sim \mathcal{N}(0,1)$ |
| Normal                     | Small                      | Unknown with $\sigma_X=\sigma_Y$      | $\frac{(\bar{X}-\bar{Y})-\delta}{s\sqrt{\frac{1}{n_X}+\frac{1}{n_Y}}} \sim \mathcal{t}_{n_X+n_Y-2}$ |



## The Wald Test

Let $θ$ be a scalar parameter, let $\hat{θ}$ be an estimate of $θ$ and let $􏰸\hat{se}$ be the estimated standard error of $θ$.

​	Note: $\hat{se} \approx \frac{s}{n}$, where $s$ is sample standard deviation $s=\sqrt{\frac{\sum{(x-\bar{x})^2}}{n-1}}$

**Definition.** The Wald Test

​	*Consider testing*
$$
H_0 :θ=θ_0 \text{ versus } H_1 :θ \neq θ_0.
$$
​	*Assume that θ is asymptotically Normal:*
$$
\frac{(\hat{θ}−θ_0)}{\hat{se}} \sim 􏱂N(0,1)
$$
​	*The size α of **Wald test** is: reject $H_0$ when $|W | > z_{α/2}$ where*
$$
W=\frac{(\hat{θ}−θ_0)}{\hat{se}} 
$$


## P-Value

**p-values** are numbers $\in [0,1]$ that quantify how confident we should be that A is different from B.

> How ***small*** does a **p-value** have to be before we are sufficiently confident that A is different from B?

- A commonly used threshold is **0.05**. It means 
  - If there is no difference between A and B, and if we did this exact sample experiment a bunch of times, then only **5%** of those experiments would result in the ***wrong*** decision; Or
  - If there is no difference between A and B, **5%** time we do the experiment, we will get a **p-value** less than **0.05**, aka a **False Positive**. 

**False Positive**: getting a small **p-value** where there is no difference 



**Another e.g.:** 

- Using a threshold of **0.00001** means we would only get a **False Positive** once every **100,000** experiments.
- Using a threshold of **0.2**  means we would only get a **False Positive** **2** times out of **10**.



**Important**:

- If we calculate a **p-value** < 0.05 then we will decide that A is different from B $\rightarrow$ we should reject the **Null Hypothesis**

- While a small **p-value** helps us decide if A is different from B, it does ***not*** tell us ***how different*** they are or effective size.



**Two types of p-value:**

1. **One-sided**: rarely used and potentially dangerous
2. **Two-sided**



### Calculating P-Value

**p-values** are determined by adding up probabilities. 3 parts:

1. The probability random chance would result in the observation
2. The probability of observing something else that **is eqaully rare**
3. The probability of observing something **rarer** or more extreme

Why adding the latter 2 parts? -  A lot of equally rare or rarer things would make something less special



e.g. the p-value for getting 4 Heads and 1 Tails

> 5/32+5/32+2/32=0.375





### P-Hacking

**P-Hacking** refers to the misues and abuse of analysis techniques and results in being fooled by **false positives**.

- **Multiple Testing Problem**: doing a lot of tests and ending up with **False Positives**.
  - **False Discovery Rate**: don't just collect all the data but only calculate a **p-value** for the one time things look different, instead, calcualte a **p-value** for each test and adjust all of the **p-values** with **FDR**.

- When a **p-value** is close to **0.05**, there is a high prob that just adding one new measurement to both groups will result in a **false positive**. -> don't give each group more data points
  - **Power Analysis:** performed before doing an experiment and tells us how many replicates we need in order to have a relatively high probability of ***correctly*** rejecting the **null hypothesis**.



## Statistical Power

**Power** is the probability that we will *correctly* reject the **Null Hypothesis**, i.e. get a small **p-value**.

- When we have 2 distributions that have very little overlap, we have a lot of **Power** because there is a high prob that we will *correctly* reject the ***null hypothesis***.
- When the 2 distributions overlap a lot, and if we have a small sample size, we will have small **Power**
- If we want more **Power**, we can increase the sample size

**Power Analysis**: tell us how many measurements we need to collect to have a good amount of **Power**.





## Power Analysis

**Power Analysis**: determines what sample size will ensure a high probability that we  *correctly* reject the ***null hypothesis*** that there is no difference between the 2 groups.

**Power Analysis** is affected by 2 factors:

1. How much overlap there is between the 2 distibutions we want to identify with our study

![](1.png)

2. The **Sample Size**

![](2.png)

> The more overlap between the 2 distributions, the larger then **Sample Size** needs to be in order to have a large **Power**. 

![](3.png)



When we increase the **Sample Size** , we have more confidence that the **estimated** means are close to the **Population Means** because extreme observations have less effect on the location of the estimated means. And the closer the estimated means are to the **Population Means** , the less the measn from the different distributions will overlap and that increase the prob that we will  ***correctly*** reject the **Null Hypothesis**.





**3 components to calculate Sample Size:** 

1. Decide how much **Power** we want.
   
   - Common value for **Power**: **0.8** $\rightarrow$ we want an **80%** prob that we will correctly reject the **Null Hypothesis**.
2. Determine the threshold for significance (called **alpha**, $\alpha$)
   
- Common value for **Alpha**: **0.05** 
   
3. Estimate the **Overlap** between the 2 distributions

   - **Overlap** is effected by both the **distance** between the population means, and the **standard deviations**  $\rightarrow$ combine them together  $\rightarrow$ **Effective Size**
   - **Effective Size (d)** = $\frac{The\ estimated\ difference\ in\ the\ means}{Pooled\ esitmated\ standard\ deviations} = \sqrt{\frac{s_1^2+s_2^2}{2}}$  where $s_1$ & $s_2$ represent the estimated standard devation for the 2 distributions.

   ![](5.png)

   

   Then  $\rightarrow$ statistics power calculator   $\rightarrow$  sample size = N

   It means if I get N measurements per group, I will have an **80%** chance that I will ***correctly*** reject the **Null Hypothesis**.





# Covariance and Correlation

**Covariance:** $Cov(X,Y)=\sigma_{XY}=E[(X-\mu_X)(Y-\mu_Y)]=E(XY)-\mu_X\mu_Y$ 

- If X and Y are discrete random variables with joint support S, then the covariance of X and Y is:
  $$
  Cov(X,Y)=\sum\sum_{(x,y)\in S}(x-\mu_X)(y-\mu_Y)f(x,y)
  $$

- If X and Y are continuous random variables with supports S1 and S2, respectively, then the covariance of X and Y is:
  $$
  Cov(X,Y)=\int_{S_2}\int_{S_1}(x-\mu_X)(y-\mu_Y)f(x,y)dxdy
  $$

**Correlation Coefficient**: $\rho_{XY}=Corr(X,Y)=\frac{Cov(X,Y)}{\sigma_X\sigma_Y}=\frac{\sigma_{XY}}{\sigma_X \sigma_Y} \in [0,1]$ 

**Independent**: If X and Y are independent random variables (discrete or continuous!), then:
$$
Corr(X,Y)=Cov(X,Y)=0
$$
**Why Coveriance is hard to interpret?** Covariance is sensitive to the scale of the data while correlation is not affected by the scale of the data.

**Correlation - p-value relationship**: For **correlation**, a **p-value** tells us the probability that randomly drawn dots will result in a similarly *strong* relationship or stronger. Thus, the smaller the **p-value**, the more confidence we have in the predictions we make with the line. We quantify the confidence of correlation with a **p-value**. The more data we have, the more confidence (smaller p-value) we have.

**Why Correlation is still hard to interpret?** It's not obvious that Corr=0.7 is twice as good at making predictions as Corr=0.5, while $R^2=0.7$ is 1.4 times as good as $R^2=0.5$





# ANOVA

## Basic Ideas

**Why ANOVA?**: the previous hypothesis tests are only about a maximum of 2 populations, ANOVA permits comparisons of multiple populations and even subgroups.

**Null Hypothesis:** whether or not these 3 sample means come from the same population.
$$
H_0: \mu_1=\mu_2=\mu_3
$$
![](anova1.png)



In ANOVA, the ideas below are very important.

- **Variability AMONG/BETWEEN the sample means**. each sample mean's distance from the mean of the overall population.
- **Variability AROUND/WITHIN the sample means**. the variance or SPREAD of each distribution.

![](anova5.png)



**Definition**. **ANOVA** is a *variability ratio*
$$
\frac{\text{Variability AMONG/BETWEEN the means}}{\text{Variability AROUND/WITHIN the distributions}}
$$
![](anova3.png)

> If the variabitlity BETWEEN the means (distance from overall mean) in the numerator is relatively large compared to the variance WITHIN the samples (internal spread) in the denominator, the ratio will be much larger than 1. The samples then most likely do NOT come from a common population; REJECT Null Hypothesis that mean(s) are equal.

![](anova4.png)



## One-Way ANOVA

**Formulas For One-Way ANOVA**:
$$
\text{Sum of Squares Total(SST)}=\text{Sum of Squares Between(SSC)}+\text{Sum of Squares Within(SSE)}
$$

$$
\begin{align}
N=\text{total observations} \quad C=\text{Number of columns/treatments/tests}  \\
\end{align}
$$

|                                            | Degree of Freedom | Average                                      | Formula                                                |
| ------------------------------------------ | ----------------- | -------------------------------------------- | ------------------------------------------------------ |
| $\text{Sum of squares(columns)(SSC)}$      | $C-1$             | $\text{MSC}=\frac{SSC}{\text{df}_{columns}}$ | $\sum_{j=1}^kn_j(\bar{x}_j-\bar{\bar{x}})^2$           |
| $\text{Sum of squares(within/error)(SSE)}$ | $N-C$             | $\text{MSE}=\frac{SSE}{\text{df}_{error}}$   | $\sum_{j=1}^k(\sum_{i=1}^{n_1}(x_{ji}-\bar{x}_j)^2)$   |
| $\text{Sum of squares(total)(SST)}$        | $N-1$             | $\text{F}=\frac{MSC}{MSE}$                   | $\sum_{j=1}^k\sum_{i=1}^{n_j}(x_{ji}-\bar{\bar{x}})^2$ |



**Example**:


```python
df=pd.DataFrame(data=np.array([[82,93,61,74,69,70,53],[71,62,85,94,78,66,71],[64,73,87,91,56,78,87]]).T,
                columns=['year_1','year_2','year_3'])
df
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }

</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>year_1</th>
      <th>year_2</th>
      <th>year_3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>82</td>
      <td>71</td>
      <td>64</td>
    </tr>
    <tr>
      <th>1</th>
      <td>93</td>
      <td>62</td>
      <td>73</td>
    </tr>
    <tr>
      <th>2</th>
      <td>61</td>
      <td>85</td>
      <td>87</td>
    </tr>
    <tr>
      <th>3</th>
      <td>74</td>
      <td>94</td>
      <td>91</td>
    </tr>
    <tr>
      <th>4</th>
      <td>69</td>
      <td>78</td>
      <td>56</td>
    </tr>
    <tr>
      <th>5</th>
      <td>70</td>
      <td>66</td>
      <td>78</td>
    </tr>
    <tr>
      <th>6</th>
      <td>53</td>
      <td>71</td>
      <td>87</td>
    </tr>
  </tbody>
</table>

</div>




```python
N=len(df)*len(df.columns)
C=len(df.columns)
N,C
```


    (21, 3)


```python
mean_1=df['year_1'].mean()
mean_2=df['year_2'].mean()
mean_3=df['year_3'].mean()
mean_total=(df['year_1'].sum()+df['year_2'].sum()+df['year_3'].sum())/N
mean_1,mean_2,mean_3,mean_total
```


    (71.71428571428571, 75.28571428571429, 76.57142857142857, 74.52380952380952)


```python
SSC = ((mean_1-mean_total)**2 +
      (mean_2-mean_total)**2 +
      (mean_3-mean_total)**2)*7

SSE = (sum((df['year_1']-mean_1)**2) +
       sum((df['year_2']-mean_2)**2) +
       sum((df['year_3']-mean_3)**2))
SST=SSC+SSE
SSC,SSE,SST
```


    (88.66666666666693, 2812.571428571429, 2901.238095238096)


```python
MSC=SSC/(C-1)
MSE=SSE/(N-C)
F=MSC/MSE
MSC,MSE,F
```


    (44.333333333333464, 156.25396825396828, 0.28372612759041116)


```python
F_crit=scipy.stats.f.ppf(q=1-0.05, dfn=C-1, dfd=N-C)
print("Since our F value({0:.2f}) is smaller than F critical value({1:.2f}), we fail to reject the null hypothesis. So there is no significant difference between the mean of each column".format(F,F_crit))

```

    Since our F value(0.28) is smaller than F critical value(3.55), we fail to reject the null hypothesis. So there is no significant difference between the mean of each column



## Two-Way ANOVA

**Two-Way ANOVA "Block" Design**:

ANOVA is about partitioning the total  / overall variance into **different parts**; assigning parts of the overall variance to different sources.

One of those parts is always ERROR; the unexplained source.

In a One-Way ANOVA, aside from ERROR, we were only working with one potential source of variance: COLUMNS/GROUPS.

A Two-Way ANOVA allows us to "account for variation" at the ROW level due to some other **factor** or **grouping** . We introduce a new way to separate the data: BLOCKS

- Blocks allow us to further refine how we "assign" or split apart the overall variance, allowing for more powerfil hypothesis tests.
- By adding blocks or factors to the ROWS, we can "subtract out" that ROW variance from the overall ERROR variance.
- This allows greater focus on COLUMN or GRROUP differences *making it easier to **detect** group differences.*

![](anova6.png)



**Eating up original SSE with Blocks**:

> SSC wants SSE to be as small as possible. "Hey SSB, eat up original SSE!"

> In the end, SSC will be compared to SSE. So, the smaller SSE is, SSC can claim a larger part of SST.



**Formulas For One-Way ANOVA**:
$$
\begin{align}
\text{Sum of Squares Total(SST)}=&\text{Sum of Squares Between(SSC)}+ \\
				&\text{Sum of Squares Block(SSB)}+ \\
				&\text{Sum of Squares Within(SSE)}
\end{align}
$$

$$
\begin{align}
N=\text{total observations} \quad C=\text{Number of  columns/treatments/tests} \quad B=\text{Number of  blocks} \\
\end{align}
$$

|                                            | Degree of Freedom | Average                                      | Formula                                                |
| ------------------------------------------ | ----------------- | -------------------------------------------- | ------------------------------------------------------ |
| $\text{Sum of squares(columns)(SSC)}$      | $C-1$             | $\text{MSC}=\frac{SSC}{\text{df}_{columns}}$ | $\sum_{j=1}^kn_j(\bar{x}_j-\bar{\bar{x}})^2$           |
| $\text{Sum of squares(block)(SSB)}$        | $B-1$             | $\text{MSB}=\frac{SSB}{\text{df}_{blocks}}$  | $\sum_{b=1}^Bn_b(\bar{x}_b-\bar{\bar{x}})^2$           |
| $\text{Sum of squares(within/error)(SSE)}$ | $(C-1)(B-1)$      | $\text{MSE}=\frac{SSE}{\text{df}_{error}}$   | $SST-SSB-SSC$                                          |
| $\text{Sum of squares(total)(SST)}$        | $N-1$             | $\text{F}=\frac{MSC}{MSE}$                   | $\sum_{j=1}^k\sum_{i=1}^{n_j}(x_{ji}-\bar{\bar{x}})^2$ |

**Example**:


```python
df=pd.DataFrame(data=np.array([[75,70,50,65,80,65],[75,70,55,60,65,65],[90,70,75,85,80,65]]).T,
                columns=["city_{}".format(i) for i in range(1,4)],index=["shopper_{}".format(i) for i in range(1,7)])
df
```


<div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }


    .dataframe tbody tr th {
        vertical-align: top;
    }
    
    .dataframe thead th {
        text-align: right;
    }

</style>

<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>city_1</th>
      <th>city_2</th>
      <th>city_3</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>shopper_1</th>
      <td>75</td>
      <td>75</td>
      <td>90</td>
    </tr>
    <tr>
      <th>shopper_2</th>
      <td>70</td>
      <td>70</td>
      <td>70</td>
    </tr>
    <tr>
      <th>shopper_3</th>
      <td>50</td>
      <td>55</td>
      <td>75</td>
    </tr>
    <tr>
      <th>shopper_4</th>
      <td>65</td>
      <td>60</td>
      <td>85</td>
    </tr>
    <tr>
      <th>shopper_5</th>
      <td>80</td>
      <td>65</td>
      <td>80</td>
    </tr>
    <tr>
      <th>shopper_6</th>
      <td>65</td>
      <td>65</td>
      <td>65</td>
    </tr>
  </tbody>
</table>

</div>


```python
N=len(df)*len(df.columns)
C=len(df.columns)
B=len(df)
N,C,B
```


    (18, 3, 6)


```python
block_means=df.mean(axis=1)
column_means=df.mean(axis=0)
total_mean=df.values.mean()
print("Means of block")
print(block_means)
print()
print("Means of columns")
print(column_means)
print()
print("Total mean:",total_mean)
```

    Means of block
    shopper_1    80.0
    shopper_2    70.0
    shopper_3    60.0
    shopper_4    70.0
    shopper_5    75.0
    shopper_6    65.0
    dtype: float64
    
    Means of columns
    city_1    67.5
    city_2    65.0
    city_3    77.5
    dtype: float64
    
    Total mean: 70.0

```python
SST=sum((df.values.reshape(-1)-total_mean)**2)
SSC=sum((column_means-total_mean)**2)*len(df)
SSB=sum((block_means-total_mean)**2)*len(df.columns)
SSE=SST-SSB-SSC
SST,SSC,SSB,SSE
```


    (1750.0, 525.0, 750.0, 475.0)


```python
MST=SST/(N-1)
MSC=SSC/(C-1)
MSB=SSB/(B-1)
MSE=SSE/(C-1)/(B-1)
MST,MSC,MSB,MSE
```


    (102.94117647058823, 262.5, 150.0, 47.5)


```python
F=MSC/MSE
F_=MSB/MSE
F,F_
```


    (5.526315789473684, 3.1578947368421053)


```python
F_crit=scipy.stats.f.ppf(q=1-0.05, dfn=C-1, dfd=(C-1)*(B-1))
print("Since our F value({0:.2f}) is larger than F critical value({1:.2f}), we can reject the null hypothesis. So significant difference do exist in cities.".format(F,F_crit))

```

    Since our F value(5.53) is larger than F critical value(4.10), we can reject the null hypothesis. So significant difference do exist in cities.









**Ref**

https://cnx.org/contents/6Znhbn2_@1.5:7mUmR30Q@1/Central-Limit-Theorem-Assumptions-and-Conditions

https://github.com/wzchen/probability_cheatsheet/blob/master/probability_cheatsheet.pdf

[https://en.wikipedia.org/wiki/Student%27s_t-distribution](https://en.wikipedia.org/wiki/Student's_t-distribution)

https://stanford.edu/~shervine/teaching/cme-106/cheatsheet-statistics#hypothesis-testing

https://www.youtube.com/user/joshstarmer