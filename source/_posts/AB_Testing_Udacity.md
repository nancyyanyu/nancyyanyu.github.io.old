---
title: Study Notes of Udacity A/B Testing
categories: Product Sense
mathjax: true
comments: true
tags: ab-testing
abbrlink: 17c5bb19
date: 2020/07/03 14:24:21
---

Study note of Udacity [A/B Testing course](https://classroom.udacity.com/courses/ud257).

<!--more-->



# Overview

A/B testing is a methodology for testing product changes. You split your users to two groups - the control group which sees the default feature, and an experimental group that sees the new features. 



## What A/B testing ***isn’t*** good for

A/B testing is not good for **testing new experiences**. It may result in *change aversion* (where users don’t like changes to the norm), or a *novelty effect* (where users see something new and test out everything).

The two things with new experiences is 

- having a baseline and 
- how much time needs to be allowed for the users to adapt to the new experience, so you can say what is going to be the plateaud experience and make a robust decision.

Finally, A/B testing cannot tell you **if you are missing something**.

In these cases, user logs can be used to develop hypothesis that can then be used in an A/B test. A/B testing gives broad quantitiative data, while other techniques such as user research, focus groups, human evaluation give you deep qualitative data



## Metric Choice

**Click-Through Rate**: $\frac{Number \ of \ clicks }{Number \ of \ page views}$

- Use a rate when you want to measure the **usability** (how often do they actually find that button.)



**Click-Through Probability** $\frac{Unique \ visitors \ who \ click }{Unique \ visitors \ to \ page}$

- Use a probability when you want to measure the **total impact** (how often users went to the second level page on your site)



>  We're interested in whether users are progressing to the second level of the funnel, which is why we picked a probability.



### How to compute rate & probability ? 

Rate: on every page view you capture the event, and then whenever a user clicks you also capture that click event

- Sum the page views, you sum the clicks and you divide.

Probability: match each page view with all of the child clicks, so that you count, at most, one child click per page view.



## Review Distribution

### Binomial Distribution

For a binomial distribution with probability $p$ , the mean is given by $p$ and the standard deviation is $\sqrt{p \times (1−p)/N}$ where $N$ is the number of trials. 

A binomial distribution can be used when

1. The outcomes are of 2 types
2. Each event is independent of the other
3. Each event has an identical distribution (i.e. $p$ is the same for all)

> We expect click-through probability to follow a binomial distribution



### Confidence Interval

Click or non-click

$x$: # of users who clicked

$N$: # of users

$\hat{p}=\frac{x}{N}$ : estimate of probability

- $\hat{p}=\frac{100}{1000}=0.1$

To use normal: check $N \cdot \hat{p}>5$ and $N \cdot (1-\hat{p})>5$

$z$- distribution: standard normal distribution with $\mu=0$ and $\sigma=1$ 

- With 95% confidence, the true value would be within $1.96 $ and $-1.96$

$m=z \cdot SE=z \cdot \sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$: margin of error

- $m=0.019$, 
- Confidence interval: $[0.081,0.119]$

This means, if you'd run the experiment again with another 1000 page views, you'd maybe expect to see between 80 and 120 clicks, but more or less than that would be pretty surprising.



#### Standard deviation of binomial

If you look up a binomial distribution elsewhere, you may find that it has a mean of $np$ and a standard deviation of $\sqrt{np(1-p)}$. This is for a binomial distribution defined as **the total number of successes**, whereas we will use **the fraction or proportion of successes** throughout this class. In this cas, the mean is $p$ and standard deviation is $\sqrt{\frac{p(1-p)}{n}}$.

#### Useful equations

You may find these equations helpful in solving the quiz:
![p_hat = X/N  SE = sqrt(p_hat (1-p_hat) / N)  m = z* SE](https://lh3.googleusercontent.com/7G4sR5EnVaKfqmc98cJorT0F9TtKIYEql7WDIYuemeWSGcGf6fW_MqrxR8fAs39n6gdmZ2ubbg28ttH_O9c=s0#w=134&h=128)



## Hypothesis Testing

**null hypothesis:** there's no difference in click-through probability between our control, and our experiment.

**alternative hypothesis:** whether the click-through rate is different? Or it's higher, or lower? Or are we interested in any kind of difference at all?

### Two-tailed vs. one-tailed tests

Two-tailed: The null hypothesis and alternative hypothesis allows you to distinguish between three cases:

1. A statistically significant positive result
2. A statistically significant negative result
3. No statistically significant difference.

 one-tailed: allows you to distinguish between two cases:

1. A statistically significant positive result
2. No statistically significant result

Which one you should use depends on what action you will take based on the results. If you're going to launch the experiment for a statistically significant positive change, and otherwise not, then you don't need to distinguish between a negative result and no result, so a one-tailed test is good enough. If you want to learn the direction of the difference, then a two-tailed test is necessary.



## Comparing two samples - Pooled Standard Error

For comparing two samples, we calculate the **pooled standard error**. For e.g., suppose $Xcont$ and $Ncont$ are the control number of users that click, and the total number of users in the control group. Let $X_{exp}$ and $N_{exp}$ be the values for the experiment. 

The pooled probability is given by

$\hat{p}_{pool}=\frac{X_{cont}+X_{exp}}{N_{cont}+N_{exp}}$

$$SE_{pool}=\sqrt{\hat{p}_{pool}∗(1−\hat{p}_{pool})∗(\frac{1}{N_{cont}}+\frac{1}{N_{exp}})}$$

$$\hat{d}=\hat{p}_{exp}−\hat{p}_{cont}$$

$$H_0:d=0 \ where \ \hat{d} ∼N(0,SE_{pool})$$



$d$ is practical significance boundary. If $\hat{d} >1.96∗SE_{pool}$ or $ \hat{d}<−1.96∗SE_{pool}$ then we can reject the null hypothesis and state that our difference represents a statistically significant difference



### Practical or Substantive Significance

Practical significance is the level of change that you would expect to see from a business standpoint for the change to be valuable. 

The differences in the magnitudes for what's consider practically significant can be quite different. What you really want to observe is repeatability. 

Statistical significance is about **repeatability**. And you want to make sure when you setup your experiment that you get that guarantee that yes, these results are repeatable so it's statistically significant.

The statistical significance bar is often lower than the practical significance bar, so that if the outcome is practically significance, it is also statistically significant.



## Size vs Power trade-off

**Statistical power:** given that we have control over how many page views go into our control and our experiment, we have to decide how many page views we need in order to get a statistically significant result. ->  determine the number of data points needed to get a statistically significant result. 

**Power** has an inverse trade-off with **size**. The smaller the change you want to detect or the increased confidence you want to have in the result, means you have to run a larger experiment.



As you increase the number of samples, the confidence interval moves closer to the mean.

$$α=P(reject\ null | null\ true)$$  - Falsely concluding there is a difference.

$$β=P(fail\ to\ reject\ null | null\ false)$$  - Falsely concluding there is no difference

$1−β$ is referred to as the **sensitivity** of the experiment, or **statistical power**. In general, you want your experiment
to have a high level of sensitivity at the practical significance boundary. People often choose high sensitivity, typically around *80%*.



For a small sample, 

- $α$ is low (you are unlikely to launch a bad experiment) 
- $β$ is high (you are likely to fail to launch an experiment that actually did have a difference you care about). 

For a large sample,

- $α$ remains the same 
- $β$ is lower (i.e. sensitivity increases).



As you change one of the parameters, your sample size will change as well. 

- If you increase the baseline click through probability (under 0.5) then this increases the standard error, and therefore, you need a higher number of samples
- If you increase the practical significance level ($d_{min}$), you require a fewer number of samples since larger changes are easier to detect
- If you increase the confidence level ($1-\alpha$), you want to be more certain that you are rejecting the null. At the same sensivitiy, this would require increasing the number of samples
- If you want to increase the sensitivity ($1-\beta$), you need to collect more samples



### Analyze Results

```python
N_cont = 10072  # Control samples (pageviews)
N_exp = 9886  # Test samples (pageviews)
X_cont = 974  # Control clicks
X_exp = 1242  # Exp. clicks

p_pool = (X_cont + X_exp)/(N_cont+N_exp)
se_pool = sqrt(p_pool*(1-p_pool)*(1/N_cont + 1/N_exp))

p_cont = X_cont/N_cont
p_exp = X_exp/N_exp
d_hat = p_exp - p_cont
# d_hat = 0.02892847

m = 1.96*se_pool
cf_min = d_hat-m
cf_max = d_hat+m
d_min = 0.02 # Minimum practical significance value for difference
# cf_min = 0.0202105
# cf_max = 0.03764645
```

Since the minimum confidence limit is greater than 0 and the practical significance level of 0.02, we conclude that it is highly probable that click through probability is higher than 0.02 and is significant. Based on this, one would launch the new version.



# Choosing and Charaterizing Metrics

:)

## Metric Definition

**Two use cases of metrics:**

- ***Invariant checking*** (sanity checking): Metrics that shouldn’t change between your test and control 
  - Do you have the same number of users across the two?
  - Is the distribution the same?
- ***Evaluation***: 
  - High level business metrics: how much revenue you make, what your market share is, how many users you have 
  - Detailed metrics: user experience with the product

**How to make a definition**:

1. A high level concept for a metric/ one sentence summary: "active users", "click-through probability"
2. Nitty gritty details: How do you define what active is? Which events count towards activity?
3. Summarize individual data measurements into a single metric: a sum or a count, an average

For evaluation, you can choose either one metric or a whole suite of metrics. 

**Overall Evaluation Criterion (OEC):** a term that Microsoft uses for when they come up with a weighted function that combines all of these different metrics.

- Hard to define and get everyone to agree
- Over-optimize
- Hard to explain why is it moving

**How generally applicable the metric is**:

If you're running a whole suite of AB tests, then ideally you'd have one or more metrics that you can use across the entire suite.  If you are running a suite of A/B tests, it is preferable to have a metric that works across the entire suite. It's much better to use a metric that's **less optimal** than it is to come up with the perfect metric for your test.



### Refining the Customer Funnel

User funnel indicates a series of steps taken by users through the site. It is called a funnel because every subsequent stage has fewer users than the stage above. 

Each stage is a metric: 

- Count: No. of users who reach that point (keep in certain stages)

- Rate: better for usability test. You want to increase rate of progression
- Probability: progression. A unique user progressed down the funnel





## Defining Metrics: Other Techniques

1. **External Data:** great for brainstorming, think of new metrics idea; good for validating metrics; benchmark your own metrics against the industry; help you develop validation techniques
   - Companies that collect data (e.g. Comscore, Nielsen)
   - Companies that conduct surveys (e.g. Pew)
   - Academic papers
2. Internal Data:
   - **Retrospective analysis**:
     - Look at historic data to look at changes and see the evaluation
     - Good to get a baseline and help you develop theories
   - **Surveys and User experience research**: 
     - help you develop ideas on what you want to research

The problem of these studies: show you correlation, not causation



### **Gathering Additional Data**

1. **User Experience Research (UER)**: 

   - high depth on a few users.
   - good for brainstorming. 
   - can use special equipment in a UER (e.g. eye movement camera) 

     X validate the results (retrospective analysis)

2. **Focus groups**: 

   - Medium depth and medium # of participants. 
   - Get feedback on hypotheticals

     X may run into the issue of groupthink

3. **Surveys**:

   - low depth but high # of participants
   - Useful for metrics you cannot directly measure. 

     X Can’t directly compare with other metrics since population for survey and internal metrics may be different.



### Applying Other Techniques on Difficult Metrics

**Difficult Metrics:**

1. Don't have access to data
2. Takes too long



**Rate of returning for 2nd course**

- Takes too long
- Survey -> proxy

**Average happiness of shoppers**

- Don't have access to data
- Survey; UER - brainstorm

**Probability of finding info via search**

- Don't have access to data
- External data; UER; Human raters
- Possible proxies: time spent; clickes on result; follow up queries

## Metric definitions & Data Capture

Def #1 (Cookie probability): For each <time interval>, number of cookies that click divided by number of cookies

Def #2 (Pageview probability): Number of pageviews with a click within <time interval> divided by number of pageviews

Def #3 (Rate): Number of clicks divided by number of pageviews



### Filtering and Segmenting

> Good for evaluating definitions and building intuitions

You may have to filter out spam and fraud to de-bias the data. You do want to be careful you don't introduce bias into your data by doing the filtering.

One way to figure out if you are biasing or de-biasing the data by filtering, is to **slice your data** by country, or by language, or by platform, and then calculate the metric for each slice after filterig. If you are affecting any slide disproportionately, then you may be biasing your data with filtering

To remove any weekly effects when looking say at total active cookies over time, use week-over-week i.e. divide current data by data from a week ago. Alternately, one can use year-over-year.

## Summary Metrics

>  Summarize all of these individual data measurements into a single summary metric.

**Characteristics for your metric**:

1. the **sensitivity** and **robustness**: You want your metric to be sensitive enough, in order to actually detect a change when you, when you're testing
   your possible future options,
2. the **distribution**: The most ideal way of doing this is to do a retrospective analysis to compute a histogram.

**4 categories of summary metrics:**

1. Sums and counts: # of users who visited
2. Distributional metrics: the means, the medians, the 25th, the 75th and 90th percentiles.

3. Probabilities and rates.
4. Ratios: can compute a whole range of different business models, and various different things that you may care about, but they can be very
   difficult to characterize.

## **Sensitivity** and **Robustness**

 Whether the metric is sensitive to changes you care about, and is robust to changes you don’t care about

- **mean** is sensitive to outliers - NOT robust
- **median** is robust but not sensitive to changes to small group of users

How to measure **Sensitivity** and **Robustness**:

1. **A/A tests** to see if the metric picks up any spurious differences
2. Using *prior experiments* to see if the metric moves in a way that intuitively make sense. 
3. *Retrospective analysis* of log data: look back at changes you made to your website and see if the metrics you're interested in actually moved in conjunction with chose changes.

## Absolute vs. Relative difference

Suppose you run an experiment where you measure the number of visits to your homepage, and you measure 5000 visits in the control and 7000 in the experiment. Then the absolute difference is the result of subtracting one from the other, that is, 2000. The **relative difference** is the absolute difference divided by the control metric, that is, 40%.

If you are running a lot of experiments you want to use the relative difference i.e the percentage change. 

- The main advantage : you only have to choose one *practical significance boundary* to get stability over time rather than change it as the system changes. 
- The main disadvantage : **variability**, relative differences such as *ratios* are not as well behaved as absolute differences

###### Relative differences in probabilities

For probability metrics, people often use percentage points to refer to absolute differences and percentages to refer to relative differences. For example, if your control click-through-probability were 5%, and your experiment click-through-probability were 7%, the absolute difference would be 2 percentage points, and the relative difference would be 40 percent. However, sometimes people will refer to the absolute difference as a 2 percent change, so if someone gives you a percentage, it's important to clarify whether they mean a relative or absolute difference!



## Variability

We want to check the variability of a metric to later determine the sizing of the experiment and to analyze confidence intervals and draw conclusions. If we have a metric that varies a lot, then the practical significance level that we are looking for may not be feasible.

To calculate the confidence interval, you need

- Variance (or standard deviation)
- Distribution

Binomial distribution:

$$SE=\sqrt{\frac{\hat{p}(1-\hat{p})}{N}}$$

$$m=z^*SE$$

We use the fact that this was a binomial distribution in two ways.

1. we use the fact that this was a binomial distribution to get this formula for the standard error.

2. this formula for the margin of error depends on the assumption that this is a normal distribution, as the binomial approaches a normal distribution as N gets larger.

| Type of metric                                               | Distribution                                   | Estimated variance                                           |
| ------------------------------------------------------------ | ---------------------------------------------- | ------------------------------------------------------------ |
| Probability                                                  | Binomial (normal)                              | $\frac{\hat{p}(1-\hat{p})}{N}$                               |
| Mean                                                         | Normal                                         | $\frac{\hat{\sigma}^2}{n}$ ($\hat{\sigma}$ : variance of the sample) |
| Medium/percentile                                            | Depends on the assumption of data distribution | Depends                                                      |
| Count/difference                                             | Normal (maybe)                                 | $Var(X)+Var(Y)$                                              |
| Rates                                                        | Poisson                                        | $\bar{X}$ (mean)                                             |
| Ratios ($\frac{\hat{p}_{exp}}{\hat{p}_{cont}}$ instead of $\hat{p}_{exp}-\hat{p}_{cont}$) | Depends                                        | Depends                                                      |

The variance of the actual metric: if you were to collect a new sample, how would you expect this metric to vary?

The variance of the sample:  take each of your data points and then collect the variance of them.



**Calculating CI for a Mean**

```python
import math
N= [87029, 113407, 84843, 104994, 99327, 92052, 60684]
N_mean=sum(N)/len(N)
N_std=math.sqrt(sum([(n-N_mean)**2 for n in N])/(len(N)-1))
SE=N_std/math.sqrt(len(N))

cf_min,cf_max=N_mean-SE*1.96,N_mean+SE*1.96
```



### Non-parametric methods

A way to analyze the data without making an assumption about what the distribution Is.

- **sign test**
- Compute variance empirically

Reason of using Non-parametric methods: 

- for more complicated metrics, you might need to estimate the variance empirically instead of computing it analytically.
- At Google, it was observed that the analytical estimates of variance was often under-estimated, and therefore they have resorted to use empirical measurements based on A/A test to evaluate variance. 

-> using **A versus A experiments** across the board to estimate the empirical variability of all of our metrics.



**What are A versus A experiments**: in an A versus A test, what you have is a control, A against another control A, and so there's actually no change
in what the users are seeing. What that means that any differences that you measure are due to the underlying variability, maybe of your system, of the user population, what users are doing, all of those types of things.

If you see a lot of variability in a metric in an A versus A test, it's probably too sensitive to be useful in, in evaluating a real experiment.

**How many A/A tests are needed to get a good sense?**: The key rule of thumb to keep in mind is that the standard deviation is going to be proportional to the square root of the number of sample.

**What if you can't run many A/A tests for some reason?**: another option is to run one really big A versus A experiment. And then using bootstrap, where what you do is you take that big sample, and you randomly divvy it up into a bunch of small samples and you do the comparison within those random subsets.

One advantage of running the lots of different A/A tests is because if your experiment system is itself complicated, it's actually a very good test of your system.

**Uses of A/A tests:**

1. Compare result to what you expect (sanity check)
2. Estimate variance empirically and use your assumption about the distribution to calculate confidence
3. Directly estimate confidence interval without making any assumption of the data



**Calculating a Confidence Interval Empirically**

```python
# click-through-probability of 40 A/A tests or bootstrap samples
import math
group1=[0.02, 0.11, 0.14, 0.05, 0.09, 0.11, 0.09, 0.1 , 0.14, 0.08, 0.09,
       0.08, 0.09, 0.08, 0.12, 0.09, 0.16, 0.11, 0.12, 0.11, 0.06, 0.11,
       0.13, 0.1 , 0.08, 0.14, 0.1 , 0.08, 0.12, 0.09, 0.14, 0.1 , 0.08,
       0.08, 0.07, 0.13, 0.11, 0.08, 0.1 , 0.11]
group2=[0.07, 0.11, 0.05, 0.07, 0.1 , 0.07, 0.1 , 0.1 , 0.12, 0.14, 0.04,
       0.07, 0.07, 0.06, 0.15, 0.09, 0.12, 0.1 , 0.08, 0.09, 0.08, 0.08,
       0.14, 0.09, 0.1 , 0.08, 0.08, 0.09, 0.08, 0.11, 0.11, 0.1 , 0.14,
       0.1 , 0.08, 0.05, 0.19, 0.11, 0.08, 0.13]
confidence_level=0.95

# assume metric is normally distributed
difference=[i-j for i,j in zip(group1,group2)]
mean=sum(difference)/len(difference)
SD=math.sqrt(sum([(i-mean)**2 for i in difference ])/(len(difference)-1))
# m=SD*z-score
m=SD*1.96
ci_max=mean+m
ci_min=mean-m

> ci_min,ci_max
> (-0.06702773846019527, 0.07552773846019528)

# no assumption of metric distribution
difference=sorted(difference)
ci_min,ci_max=difference[1],difference[-2]

> ci_min,ci_max
> (-0.06000000000000001, 0.08)
```





In summary, different metrics have different variability. The variability may be high for certain metrics which makes them useless even if they make business or product sense. Computing the variability of a metric is tricky and one needs to take a lot of care.

For a lot of analysts, a majority of the time is spent is validating and choosing a metric compared to actually running the experiment. Being able to standardize the definitions was critical in the test. When measuring latency, are you talking about when the first byte loads and when a last byte loads. Also, for latency, the mean may not change at all. The signals (e.g. slow/fast connections or browsers) causes lumps in the distribution, and no central measure works. One needs to look at the right percentile metric. The key thing is that you are build



# **Design an Experiment**

1. Choose **subject**: What are the units in the population you are going to run the test on? (unit of diversion)
2. Choose **population**: What population are you going to use (US only?)
3. Size
4. Duration



## Unit of Diversion

Commonly used units of diversion are:

1. **User identifier (id)**: Typically the username or email address used on the website. It is typically stable and unchanging. If user id is used as a unit of diversion, then it is either in the test group or the control group. User ID is personally identifiable
2. **Anonymous id**: This is usually an anonymous identifier such as a **cookie**. It changes with browser or device. People may often refresh their cookies every time they log in. It is difficult to refresh a cookie on an app or a phone compared to the computer.
3. **Event**: An event is a page load that can change for each user. This is used typically for non-user-visible changes.

Less common:

1. **Device id**: Typically available for mobile devices. It is tied to a specific device and cannot be changed by the user. Personally identifiable.
2. **IP address**: location specific, but may change as the user changes location (e.g. testing on infrastructure change to test impact on latency)

3 main considerations in selecting an appropriate unit of diversion:

1. **Consistency**
2. Ethical - Informed consent: a issue when using user id.
3. Variability



### Consistency of Diversion

If you're testing a change that crosses the sign in, sign out border, then a **user ID** doesn't work as well, use cookie instead.

**For user visible changes**, you would definitely use a **cookie** or a **user ID**. But there are lots of non-user-visible changes (latency changes, back-end infra changes or ranking changes):

Examples:

- Cookie: change button color and size (distracting if changes on reload; different look on difference devices is ok)
- User id: Add instructors note before quizzes (cross device consistency important)
- Event: Reducing video load time; change order of search results (users won't probably notice)

IP based diversion not very useful: 

- not as consistent as cookie or user id as ip randomly change depending on the provider; no clean randomization like event based diversion.
- only choice: testing out one hosting provider versus a different hosting provider to understand the impact of latency.
- May not get a clean comparison between your experiment and your control:some providers aggregate all of those modem dialup users into a single IP address, so it's hard to find comparable population of users in the control group.

### Unit of Analysis v.s. Unit of Diversion

**Variability** is higher when it is calculated empirically than when calculated analytically. This is because the **unit of analysis** (i.e. the denominator in the metric. e.g. page view in click through rate) is different from the unit of variability.

When your *unit of diversion* is also a page view, so as would be the case in an **event** base diversion, then the analytically computed variability is likely to be very close to the empirically computed variability.

If, however, your unit of diversion is a **cookie** or a user id then the variability of your same metric click through rate is actually going to be much higher.

This is because  when you're actually computing your variability analytically, you're fundamentally making an assumption about the distribution of the data and what's considered to be independent.

- When you're doing event-based diversion every single event is a different randomdraw, and so your independence assumption is actually valid.

- When you're doing cookie or user ID based diversion, that independence assumption is no longer valid because you're actually diverting groups of events. And so they're actually correlated together.

**Measure variablity of a metric**:

Unit of diversion: queries (event-based) v.s. cookies

Metric: coverage (the percentage of queries for which an ad is shown) = $\frac{\# queries\ with\ ads}{\#\ queries}$

Unit of analysis: query

Binomial: $SE=\sqrt{\frac{p(1-p)}{N}}$

The SE of coverage under cookies diversion is much larger then that of queries.

> When unit of analysis = unit of diversion, variability  tends to be lower and closer to the anlytical estimate.



## Choose Population

**intra-user experiment:** expose the same user to this feature being on and of over time, and you actually analyze how they behave in different time windows.

- pitfalls: 
  - be really careful that you choose a comparable  time window.(not 2 weeks before Christmas)
  - different behaviors as a result of  a frustration or a learning problem, where people learn to use the particular feature in the first two weeks 

**interleaved experiment**: expose the same user to the A and the B side at the same time. And typically this only works in cases where you're looking at reordering a list.

**inter-user experiments**: different people on the A side and on the B side.



### Target Population

Some easy divisions of your user space: what browser they're on, what geo location they come from, what country, what language they're using.

Reasons why you might make that decision in advance: 

- For a feature not sure if you're going to release it and it's a pretty high profile launch, you might want to restrict how many of your users have actually seen it.
- If you're running a couple of different experiments at your company at the same time, you might not want to overlap.
- You may not want to dilute the effect of your experiment across a global population. So if you're analyzing an experiment for the first time, and it only affects English, you may want to actually do your analysis specific on English, 



**Targeting an Experiment**:

> Filtering could also affect variability

```python
# New Zealand
N_cont = 6021
X_cont = 302
N_exp = 5979
X_exp = 374

p_cont=X_cont/N_cont
p_exp=X_exp/N_exp
p_pool=(X_cont+X_exp)/(N_cont+N_exp)
SE=math.sqrt((p_pool*(1-p_pool)*(1/N_cont+1/N_exp)))
d_hat=p_exp-p_cont
m=1.96*SE
> m,d_hat
> (0.00825068746366646, 0.012394485165776618)

# Global
N_cont = 50000 + 6021
X_cont = 2500 + 302
N_exp = 50000 + 5979
X_exp = 2500 + 374

p_cont=X_cont/N_cont
p_exp=X_exp/N_exp
p_pool=(X_cont+X_exp)/(N_cont+N_exp)
SE=math.sqrt((p_pool*(1-p_pool)*(1/N_cont+1/N_exp)))
d_hat=p_exp-p_cont
m=1.96*SE
> m,d_hat
> (0.0025691881506085417, 0.0013237234004343165)
```



### Population v.s. Cohort

Cohort: people who enter the experiment at the same time. You define an entering class and you only look at users who entered your experiment on both sides around the same time, and you go forward from there.

**Cohorts** are harder to analyze, limit your experiment to a subset of population, can affect variability . 

Want to use cohort instead of population:**user stability**

- Looking for learning effects
- Examining user retention
- Want to increate user activity
- Anything that require users to be established 



## Experiment Design and Sizing

**How to reduce the size of experiments:**

Experiment: change order of courses in the course list

Metric: click-through-rate

Unit-of-diversion: cookie

$d_{min}=0.05$, $\alpha=0.05$, $\beta=0.2$, $SE=0.0628$ for 1000 page views

Result: need 300,000 pageviews per group!

Which strategies could reduce the number of page views?

1. Increase $d_{min}$, $\alpha$, or $\beta$
2. Change unit of diversion to page view
   - Makes unit of diversion same as unit of analysis
   - The variability of the metric might decrease and thus decrease the number of page views you need to be confident in your results
3. Target experiment to specific traffic
   - Non-english traffic will dilute the results
   - Could impact choice of practical significance boundrey 
     - since you're only looking at a subset of the traffic, you might need a bigger change before it matters to the business; 
     - Or since your variablity is probably lower, you might want to detect smaller changes rather than decreasing the number of page views.

How are you actually going to detect impact? What effect does that have on the size of the experiment when you really don't know what fraction of your population is going to be affected?

- run a pilot where you turn on the experiment for a little while and see who's affected, or you can even just use the first day or the first week of data to try to get a better guess at what fraction of your population you're really looking at.




## Duration  v.s. Exposure

**Practical considerations in experimental design**: 

1. Duration
2. When to run the experiment
3. Fraction of the traffic to send to the experiment

The duration of your experiment, is related to the proportion of traffic that you're sending through your experiment.

**Why not run on all of the traffic to get results quicker?** 

- Safety: not sure if it's working on all browser or how the users will react
- Press: people blogging about features you might not keep
- Other sources of variablity : holiday, weekly variation
- Running multiple tasks at your company: to be comparable, run them at the same time on smaller percentages of traffic.

**Note:** There is weekly variation in traffic and metric, so it's better to run on mix of weekend and weekday days. For risky change, run longer with less traffic.





## Learning Effects

**learning effects** is basically when you want to measure user learning. Or effectively whether a user is adapting to a change or not.

Two different types of learning effects

1. Change aversion "I don't like anything."
2. Knowledge effect "Let me try everything around."

When users first encounter a change they will react in one of these two ways, but will eventually plateau to a very different behavior.

**Considerations when  measure a learning effect:**

1. The key issue with trying to is **time**： It takes time for you just to actually adapt to a change and often times you don't have the luxury of taking that much time to make a decision.

2. Choosing the unit of diversion correctly. - a cookie or a user ID
3. Dosage: because a lot of the learning is based on not just a slight time but how often they see the change. Then you probably want to be using a cohort as opposed to just a population. - choose a cohort in both the experiment and the control based on either how long they've been exposed to the change or how many times they've seen it.
4. risk and duration: both of those mean  to run it through a small proportion of your users for a longer period of time.

**pre-periods and post-periods**:

Before run your **A/B test**, you're on a *pre-period* on the exact same populations but they're receiving the exact same frequence. It's an **A/A test** on the same set of users.

In the **pre-period**, if you measure any difference between your experiment and your control populations that difference is due to something else (system variability, user variability).**Pre-period** helps you know that any difference that you measure in your experiment and control is due to the experiment, and not due to any preexisting and inherent differences in your population.

A **post-period** is saying, after I run my experiment, my control, I'm going to run another *A versus A test*. And then, what, what we can say is that if there are any differences in the experiment and the control populations after I've run my experiment, then I can attribute those differences to **user learning** that happened in the experiment period.



# Analyze Results

## Sanity Checks

**2 main types of checks:**

1. **Population sizing metrics** based on your unit of diversion
   - Check your experiment population and your control populations are actually comparable.

2. **Actual invariants:** metrics that are same in the control and experiment groups and shouldn't change when you run your experiment



**Step 1: Choosing invariant metircs**

**Step 2: Checking invariants**

```markdown
	Run experiment for 2 weeks

​	Unit of diversion: cookie

​	Total control: 64454

​	Total experiment: 61818

​	How would you figure out if the difference is within expectations?

​	Given: each cookie is randomly assigned to the control or experiment group with a prob of 0.5.
```

	1. Compute SD of binomial with $p=0.5$ of success. $SD=\sqrt{0.5 \cdot0.5/(64454+61818)}=0.0014$
 	2. Multiple by z-score to get margin of error. $m=1.96*SD=0.0027$
 	3. Compute confidence interval around 0.5. $ [0.4973, 0.5027]$
 	4. Check whether observed fraction is within interval. $64454/(64454+61818)=0.5104$ Not within interval

**Step 3: what to do**

- Talk to engineers
- Try *slicing* to see if one particular slice is weird
- Check age of cookies  -  does one group has more new cookies?



**What happens if one of your sanity checks fails**: analyzing why your sanity checks fail

1. work with your engineers to understand is there something going on with the *experiment infrastructure*, *experiment set up*, *experiment diversion*.
2. **retrospective analysis**: Try and recreate experiment diversion from the data capture,

3. use pre & post periods
   - If you're in a pre-period,did I see the same changes in those invariance in my pre-period?
     - If I saw them in the pre-period and the experiment, that points to a problem with the experiment infrastructure, the set up, something along those lines.
     - if you see the change only in your experiment but not in the pre-period, that points to something with the experiment itself (data capture or
       something along those lines).

**The most common reasons for data not matching up:** 

- data capture: Maybe the change triggers very rarely, and you capture it correctly in the experiment, but you don't capture quickly in the control.
- experiment's set up: Maybe you set up the filter for the experiment, but not the control.
- experiment system

> The key thing : if there really is a learning effect, then not very much change in the beginning, but it's increasing over time.

>  Not a learning effect:  a big change from the beginning



## Single Metric

Goal: make a business decision about whether your experiment has favorably impacted your metrics. Analytically, decide if you've observed a statistically significant result of your experiment.

**How do you decide if the change was statistically significant?**:

- Characterizing the metric, understanding how it behaves
- Use variability to estimate **how long** we needed to run the experiment for and **size** our experiment appropriately
- Use the results of last 2 steps to estimate the **variability** we need to analyze the A/B experiment 

**What if our results are not statistically significant?**:

- Break down the result into different platforms, different days of the week -> find bugs in your experiment setup & give you a new hypothesis about how people are reacting to the experiment.
- Cross checking your results with other methods, like non parametric sign tests to compare the results to what you got from your parametric hypothesis test.



### Analysis with Single Metric

**Experiment I :** change color and place of 'start now' button.

Metric: click-through-rate

Unit of diversion: cookie

$X_{cont}=352$, $N_{cont}=7370$

$X_{exp}=565$, $N_{exp}=7270$

$d_{min}=0.01$, $\alpha=0.05$, $\beta=0.2$

*Empirical SE*: $0.0035$ with 10000 page views in each group


$$
\begin{align}
&SE \sim \sqrt{\frac{1}{N_1}+\frac{1}{N_2}} \\
&\frac{0.035}{\sqrt{\frac{1}{10000}+\frac{1}{10000}}}=\frac{SE}{\sqrt{\frac{1}{7370}+\frac{1}{7270}}} \\
&SE=0.0041 \\
&\hat{d}=\hat{r}_{exp}-\hat{r}_{cont}=\frac{565}{7270}-\frac{352}{7370}=0.03  \\
&m=1.96*0.0041=0.0080 \\
&Confidence\ Interval:[0.022,0.038]
\end{align}
$$
Recommandation: **launch**

**Sign Test:**

​	Number of days: 7

​	Number of days with positive change: 7

​	If no difference, 50% chance of positive change each day

​	(7 days too short, cannot assume normal by binomial)

​	Two-tailed **p-value**: *0.0156* (the prob of observing a result at least this extreme by chance)

​	    - Since this is less than the chosen alpha of .05, the sign test agrees with the hypothesis test that this result is unlikely to happen by chance.

​	Recommandation: **launch**



**Experiment II :** 

Metric: click-through-rate

$d_{min}=0.01$, $\alpha=0.05$,

Empirical SE: $0.0062$ with 5000 page views in each group



- **Effect size:**

```python
Xs_cont = [196, 200, 200, 216, 212, 185, 225, 187, 205, 211, 192, 196, 223, 192]
Ns_cont = [2029, 1991, 1951, 1985, 1973, 2021, 2041, 1980, 1951, 1988, 1977, 2019, 2035, 2007]
Xs_exp = [179, 208, 205, 175, 191, 291, 278, 216, 225, 207, 205, 200, 297, 299]
Ns_exp = [1971, 2009, 2049, 2015, 2027, 1979, 1959, 2020, 2049, 2012, 2023, 1981, 1965, 1993]
SE_emp=0.0062
Ns_emp=5000
SE=SE_emp/math.sqrt(1/Ns_emp+1/Ns_emp)*math.sqrt(1/sum(Ns_cont)+1/sum(Ns_exp))
d_hat=sum(Xs_exp)/sum(Ns_exp)-sum(Xs_cont)/sum(Ns_cont)
m=1.96*SE
ci_min,ci_max=d_hat-m,d_hat+m
> ci_min,ci_max
> (0.006465853496236934, 0.016736185710796242)
```

Since the confidence level is larger than 0, it's statistically significant.



- **Sign test:**

```python
ctr_cont=[i/j for i,j in zip(Xs_cont,Ns_cont)]
ctr_exp=[i/j for i,j in zip(Xs_exp,Ns_exp)]

days=len(Xs_cont)
positive_days=sum([exp>cont for exp,cont in zip(ctr_exp,ctr_cont)])
> days,positive_days
> (14, 9)
```

The two-tail P value is $0.4240$. Since p-value is larger than alpha, it's not  significant.



### Single Metric: Gochas

**What if the sign test and the hypothesis test don't agree?**

Statistical reasons for counter-intuitive results: **Simpson's paradox**

> Different subgroups in the data like user populations. Within each subgroup, the results are stable but when aggregated together, it's the mix of subgroups that actually drives your result.

- New users might be correlated with weekend use, experienced users who react differently are correlated with weekday use. Within each group, their behavior is stable. What drives the results of your experiment are how many people from each group.



## Multiple Metrics

**What changes when you have multiple evaluation metrics instead of just one?**

> As you test more metrics, it becomes more likely that one of them will show a statistically significant result by chance. 

But the significant result shouldn't be repeatable. If you did the same experiment on another day or you slices or do some bootstrap analysis, you
wouldn't see the same metric showing up as significant differences every time, it should occur randomly.

**Multiple comparisons**: adjusts your significance level, so that it accounts for how many metrics or how many different tests you're doing.

- Do it when you want automatic alerting that tells you one of my metrics was significantly different on this experiment.



### Tracking Multiple Metrics

For 3 *independent* metrics, $\alpha=0.05$, the chance of at least 1 false positive is $P(FP≥1)=1-0.95^3=0.143$

$Overall\ \alpha=1-(1-\alpha)^{\#\ of\ metrics}$

**Problem:** Probability of any False Positive increases as you increase number of metrics

**Solution**: Use higher confidence level for each metric

- **Method1:** Assume *independence* of metrics

​	$\alpha_{overall}=1-(1-\alpha_{individual})^n$, calculate $\alpha_{individual}$ with specified $\alpha_{overall}$

- **Method2: Bonferroni Correction**
  - Simple
  - No assumption
  - Conservative - guarantee to give  $\alpha_{overall}$ at lease as small as specified 

  -  $\alpha_{individual}=\frac{ \alpha_{overall}}{n}$
  - <u>Problem:</u> Given metrics are correlated, and tend to move at the same time, this method is too conservative.
  - Recommendation: 
    - Rigorous answer: use a more sophisticated method like [closed testing procedure](http://en.wikipedia.org/wiki/Closed_testing_procedure), the [Boole-Bonferroni bound](http://en.wikipedia.org/wiki/Bonferroni_bound), and the [Holm-Bonferroni method](http://en.wikipedia.org/wiki/Holm–Bonferroni_method)
    - In practice: **Judgment Call**, possibly based on business strategy

**Different Strategies:** 

- Control probability that *any* metric shows a false positive $\alpha_{overall}$ -> **family wise error rate (FWER)** (Overal alpha)

- Control false discovery rate (FDR):

  $$FDR=E[\frac{\#\ false\ positives }{\#\ rejections}]$$

  - Out of all of the rejections of the null, that is, all of the metrics that you declare to have a statistically significant difference. How many of them had a real difference as opposed to how many were false positives?
  - This really only makes sense if you have a huge number of metrics, say hundreds.

e.g.: Suppose that you have 200 metrics that you're measuring and you capped the false discovery rate at 0.05. What this means is that you're okay with having 5 false positives and 95 true positives in <u>every experiment</u>. The family wise error rate, or the overall alpha in this case, would be one, since you have at least one false positive every time. But the false discovery rate is 0.05.



### Analyzing Multiple Metrics

**How do I actually make a recommendation?**

- You really have to understand how people are reacting to the change because you can't quantitatively evaluate which one is better.


- An alternative to using multiple metrics is to use an ‘Overall Evaluation Criterion’ (OEC).

**How to choose a good OEC?**

- Start with some kind of business analysis  (Our company, as a whole, wants to look at 25% revenue plus 75% increase usage of the site.)
- Run a whole bunch of different experiments and validate how they steer you.

>  Having an OIC doesn't have to be a formal number. It's really just trying to encapsulate what your company cares about. And how much you're going to be balancing something like stay time and clicks.



## Drawing Conclusion

If you have statistically significant results, now the questions come down to 

- Do you understand the change?

- Do you want to launch the change?

What if your change has a positive impact on one slice of your users, but for another slice, there's no impact or there's a negative impact?

- Is it a question about having different users, and how much they like or don't like the change? Have you seen that effect in other experiments? Do you have a bug?

**How do you decide whether to launch your change or not?**

- Do I have statistically significant and practically significant results in order to justify the change?

- Do I understand what that change has actually done with regards to user experience?
- Is it worth it?

>  **The end goal is actually making that recommendation that shows your judgment.**



## Gochas: Changes Over Time

It's good practice to always do a ramp-up when you actually want to launch a change.

The other thing is to remove all of the filters. So if you're only testing your change on English, for example, you want to test your change during your ramp-up on all users to check if there's been any incidental impact to unaffected users that you didn't test in the original experiment.



**Complication:** the effect may actually flatten out as you ramp up the change.

Reasons of the effects not being repeatable: 

1. seasonality effects, holidays
   - Capture seasonal / event-driven impacts:  **holdback**

     - You launch your change to everyone except for a small holdback (a set of users), that don't get the change, and you continue comparing their
       behavior to the control. Now, in that case you should see the reverse of the impact that you saw in your experiment. And what you can do is you can track that over time until you're confident that your results are actually repeatable.
2. **novelty effect** or **change aversion**: as users discover or adopt your change, then their behavior can change and therefore the measured effects can change.
   - **Cohort analysis** 
3. If you don't control for the budgets properly, the effect can change as you ramp up.



## Summary

1. Check that your experiment was set up properly, Look at your end variance.Check that your experiment metrics
   are actually looking sane.
2. Remember you aren't just looking for statistical significance, you're really making a business decision.
3. Can't actually forget the overall business analysis
   - Judgment call with regards to the user experience and the business: 
     - What's the engineering cost of maintaining the change?
     - Are there customer support or sales issues?
     - What's the opportunity cost if you actually choose to launch the change relative to the rewards you're going to get from the change or potentially not launching the change?





# Policy and Ethics for Experiments

## Four Principles

### 1. Risk

In the study, *what risk is the participant undertaking*? The main threshold is whether the risk exceeds that of “minimal risk”. Minimal risk is defined as the probability and magnitude of harm that a participant would encounter in <u>normal daily life</u>. 

### 2. Benefits

It is important to be able to state what the benefit would be from completing the study.

### 3. Alternatives

In online experiments, the issues to consider are what the other alternative services that a user might have, and what the switching costs might be, in terms of time, money, information, etc.

### 4. Data Sensitivity

*What data is being collected, and what is the expectation of privacy and confidentiality*? 

- For new data being collected and stored, how sensitive is the data and what are the internal safeguards for handling that data? E.g., what access controls are there, how are breaches to that security caught and managed, etc.?
- Then, for that data, how will it be used and how will participants’ data be protected? How are participants guaranteed that their data, which was collected for use in the study, will not be used for some other purpose? This becomes more important as the sensitivity of the data increases.
- Finally, what data may be published more broadly, and does that introduce any additional risk to the participants?

