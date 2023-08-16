---
title: 'A/B Testing : Pitfalls, Baysian & Math Behind'
categories: Product Sense
mathjax: true
comments: true
tags: ab-testing
abbrlink: db6945d3
date: 2020/07/16 15:22:59
---



My study note of various A/B testing resources. I do not own any credit for the any idea, knowledge, code below.

<!--more-->

# Common Pitfalls

## Network Effect

**Limitations** of per-user assignment to run an A/B test on ***user-to-user features*** or product relies heavily on interaction between users:

1. If you let the test group use video chat with anybody, the people in the control group wouldn't really be a control group because they're getting exposed to this new video chat feature.

2. Can't measure "**higher-order effects**" (also known as **network effects**)

   **Network effects**: occur when the changes induced by a new feature leak out of the test group and affect behavior in the control group as well.

   - The change would theoretically improve the experience for test group as well as the control group 
   - Higher-order effects might create an illusory change that disappears once you roll out a feature out to everybody.



### **Solution: Using per-*community* random assignment**

>  a "**community**" is any group of users whose interactions are primarily directed to other users within the same group. 

**How to define a "community"?**

Model the relationships between users with a [social graph](https://en.wikipedia.org/wiki/Social_graph), each user is a node, and edges are placed between nodes that have had some interaction. And then apply **graph partitioning** algorithms like [Normalized Cuts](https://people.eecs.berkeley.edu/~malik/papers/SM-ncut.pdf) to find isolated, non-interacting groups.

**Pitfall**: In dating apps, the community is really defined by "anybody that's near you" as opposed to "people you have a history of interacting with".



### Solution: Defining geographic communities

Define 'optimal' regions by drawing boundaries that maximizes the <u>number of connections within each region</u> relative to the <u>number of connections that occur across regions</u> using Shi & Malik's [Normalized Cut](https://people.eecs.berkeley.edu/~malik/papers/SM-ncut.pdf) algorithm for graph partitioning.

1. Build a graph to describe how many messages were sent between pairs of US/Canadian locations as the square adjacency matrix **A** (size *num_Locations*-by-*num_Locations*). The value at **A**[*i*,*j*] set to the number of messages sent between users in location *i* to users in location *j*

2. Calculate the graph *Laplacian*, **L**, from the *adjacency* matrix, **A**, and the *degree matrix*, **D**, as **L** = **D** - **A**. The degree matrix is just a diagonal matrix with the degree of each node on the diagonal and zeros everywhere else.

![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/28/6n-graph2.svg/350px-6n-graph2.svg.png),![](https://wikimedia.org/api/rest_v1/media/math/render/svg/e6f63ff7144996fb004e3d4b211c2c73c5e59fbd)

3. Calculate the eigendecomposition of **L**, and inspect its eigenvalues. Each fully-isolated (*i.e.*, disconnected) sets of nodes will be represented by an eigenvector with an associated eigenvalue of 0. Further segmentation of these regions is done by looking at the patterns in the eigenvectors with the *k* smallest, non-zero eigenvalues. The value of *k* can be varied depending on how many regions you want to make.



## History Effect

**History Effect** happens when an event from the outside world skews your testing data

 A traffic spike that’s a direct result of an unusual event means there’s a high probability that those visitors might have different needs, wants and browsing behaviours.

Because this traffic is only *temporary*, it means your test data could shift completely during this event and could result in making one of your variations win when in reality, with your regular traffic, it should’ve lost.

**Solution**

- **Prevention**: run your test for longer to get some of your usual traffic into the mix as well.
- Be aware of the fluctuations and differences in your traffic: when you’re aware of what’s happening, dig deeper in Google Analytics to analyze variations’ performance, and then recognize if your winning variation is indeed a winner.
- Launch a test that’s only targeting traffic coming from certain traffic sources (excluding the most variable traffic source from the test entirely)
- Never analyze a test solely by testing tools like [VWO](https://vwo.com/), [Optimizely](http://optimizely.com/). They don’t allow you to dig as deeply into your analysis as with *Google Analytics*: use your testing tool for running your tests. Use Google Analytics to analyze them.



## Instrumentation Effect

**Instrumentation Effect** happens when there are problems with your testing tools or test variations that cause your data to be flawed. -> companies new to testing

A common example is when the code of one or more of your variations is not functioning properly with all devices or browser types, and often, without the company who is running the test even being aware.

**Solution**

- rigorous **Quality Assurance (QA)** checks: performing cross-browser and cross-device testing on  new variations, and trying out variations under different user scenarios.

  To test on different devices and tools, you can use online tools such as [Browserstack](http://www.browserstack.com/) (which guarantees 100% accuracy for cross-browser functionalities), or [Browserling](https://www.browserling.com/).



## Selection Effect

Selection Effect: Each traffic source brings its own type of visitors, and you can’t assume that traffic from a certain channel mirrors the behaviors, context, mindset and needs of the totality of your usual traffic.

‍

*if you run an A/B test and the traffic sample is not a good representative of the average visitors to your website then you are not going to get an accurate insight on how your website visitors respond to different landing page variations (unless off course if you are running your test only for a particular traffic segment).*

**Solution**

- When you’re analyzing the test results, make sure to **segment by sources** in order to see the real data that lies behind averages.

  

## Novelty Effect

**Novelty Effect** happens when the engagement and interaction with one of your variations is substantially higher than previously, but only temporarily – giving you a false positive.

**Solution**

- Run your test for at least 4 weeks. In most cases, 4 weeks will be enough time to start seeing the novelty wear off and the test results begin to regulate.

- Keep tracking the winning variation's performance to ensure its long-term performance. 
- [analyze session recordings](https://splitbase.com/blog/conversion-research-ecommerce/#Whatrsquos_a_session_recording) and [run usability tests](https://splitbase.com/blog/conversion-research-ecommerce/#Method_2_User_Testing) in order to understand the user behavior that’s happening on your site.
- Cohort analysis: *Segment your visitors into new and returning visitors and compare the conversion rates.‍* If it’s just the Novelty Effect, the new offer will win with new visitors. Eventually, as returning visitors get accustomed to the new changes, the offer will win with them, too



## Statistical Regression

**Statistical Regression** (regression to the mean):if a variable is extreme on its first measurement, it will tend to be closer to the average on its second measurement.

> Launched an A/B test and noticed wild fluctuations during the first few days of it being live.

What this means is that if you end a test too early or based only on reaching statistical significance, you’ll likely see a false positive. 

**Solution**

- You can't avoid Statistical Regression, but can avoid to let it ruin your A/B test results by *not ending your test solely based on when you reach statistical significance*.

- Before you end a test, make sure you’ve had a large enough sample size. 



# Bayesian A/B Testing

**The Frequentist approach**: a Frequentist method makes predictions on the underlying truths of the experiment using only data from the *current* experiment.

**The Bayesian approach**: *past knowledge of similar experiments is encoded into a statistical device known as a prior, and this prior is combined with current experiment data to make a conclusion on the test at hand.*

**Difference:**

-  The biggest distinction is that Bayesian probability specifies some **prior probability**.

-  The metric you want to study  is, for the frequentists, a simple number, while for bayesians, a **distribution**.





# Math Behind A/B Testing

## Set Up The Experiment

### Baseline Conversion Rate and minimum difference between test and control

For our example, we want to use our test to confirm that the changes we make to our signup process will result in at least a 2% increase in our sign up rate. We currently sign up 10 out of 100 users who are offered a premium account.

**baseline conversion rate**: the current rate at which we sign up new users under the existing design.



```python
bcr = 0.10  # baseline conversion rate
d_hat = 0.02  # difference between the groups
```

### Control Group (A) and Test Group (B)

Initially, we will collect 1000 users for each group and serve the current signup page to the control group and a new signup page to the test group.


```python
# # A is control; B is test
N_A = 1000
N_B = 1000
```

##  Run the Test

### Generate Fake data 


```python
def generate_data(N_A, N_B, p_A, p_B, days=None, control_label='A',
                  test_label='B'):
    """Returns a pandas dataframe with fake CTR data
    Example:
    Parameters:
        N_A (int): sample size for control group
        N_B (int): sample size for test group
            Note: final sample size may not match N_A provided because the
            group at each row is chosen at random (50/50).
        p_A (float): conversion rate; conversion rate of control group
        p_B (float): conversion rate; conversion rate of test group
        days (int): optional; if provided, a column for 'ts' will be included
            to divide the data in chunks of time
            Note: overflow data will be included in an extra day
        control_label (str)
        test_label (str)
    Returns:
        df (df)
    """

    # initiate empty container
    data = []

    # total amount of rows in the data
    N = N_A + N_B

    # distribute events based on proportion of group size
    group_bern = scs.bernoulli(N_A / (N_A + N_B))

    # initiate bernoulli distributions from which to randomly sample
    A_bern = scs.bernoulli(p_A)
    B_bern = scs.bernoulli(p_B)

    for idx in range(N):
        # initite empty row
        row = {}
        # for 'ts' column
        if days is not None:
            if type(days) == int:
                row['ts'] = idx // (N // days)
            else:
                raise ValueError("Provide an integer for the days parameter.")
        # assign group based on 50/50 probability
        row['group'] = group_bern.rvs()

        if row['group'] == 0:
            # assign conversion based on provided parameters
            row['converted'] = A_bern.rvs()
        else:
            row['converted'] = B_bern.rvs()
        # collect row into data container
        data.append(row)

    # convert data into pandas dataframe
    df = pd.DataFrame(data)

    # transform group labels of 0s and 1s to user-defined group labels
    df['group'] = df['group'].apply(
        lambda x: control_label if x == 0 else test_label)

    return df
```


```python
ab_data = generate_data(N_A, N_B, bcr, bcr+d_hat)
ab_data.head()
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
      <th>group</th>
      <th>converted</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>B</td>
      <td>0</td>
    </tr>
    <tr>
      <th>1</th>
      <td>A</td>
      <td>0</td>
    </tr>
    <tr>
      <th>2</th>
      <td>B</td>
      <td>0</td>
    </tr>
    <tr>
      <th>3</th>
      <td>B</td>
      <td>0</td>
    </tr>
    <tr>
      <th>4</th>
      <td>A</td>
      <td>0</td>
    </tr>
  </tbody>
</table>

</div>



## Summary of AB data


```python
ab_summary = ab_data.pivot_table(
    index='group', values='converted', aggfunc=np.sum)
ab_summary['total'] = ab_data.pivot_table(
    index='group', values='converted', aggfunc=lambda x: len(x))
ab_summary['rate'] = ab_data.pivot_table(values='converted', index='group')
ab_summary
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
      <th>converted</th>
      <th>total</th>
      <th>rate</th>
    </tr>
    <tr>
      <th>group</th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>A</th>
      <td>107</td>
      <td>1002</td>
      <td>0.106786</td>
    </tr>
    <tr>
      <th>B</th>
      <td>132</td>
      <td>998</td>
      <td>0.132265</td>
    </tr>
  </tbody>
</table>

</div>



It looks like the difference in conversion rates between the two groups is 0.026 which is greater than the $\hat{d}$ we initially wanted of 0.02. **This is a good sign but this is not enough evidence for us to confidently go with the new design.** At this point we have not measured how confident we are in this result. This can be mitigated by looking at the distributions of the two groups. 

## Compare the Two Groups


```python
A_converted, B_converted = ab_summary['converted']['A'],ab_summary['converted']['B']
A_total,B_total = ab_summary['total']['A'],ab_summary['total']['B']
p_A,p_B = ab_summary['rate']['A'],ab_summary['rate']['B']
```


```python
fig, ax = plt.subplots(figsize=(12, 6))
xa = np.linspace(A_converted-49, A_converted+50, 100)
ya = scs.binom(A_total, p_A).pmf(xa)
xb = np.linspace(B_converted-49, B_converted+50, 100)
yb = scs.binom(B_total, p_B).pmf(xb)

ax.bar(xa, ya, alpha=0.5)
ax.bar(xb, yb, alpha=0.5)

plt.xlabel('converted')
plt.ylabel('probability')
```




    Text(0, 0.5, 'probability')




![png](Math behind_15_1.png)


We can see that the test group converted more users than the control group. We can also see that the peak of the test group results is lower than the control group. How do we interpret the difference in peak probability? We should focus instead on the conversion rate so that we have an apples-to-apples comparison. In order to calculate this, we need to standardize the data and compare the probability of successes, p, for each group. 

### Bernoulli Distribution and the Central Limit Theorem

To do this, first, consider the **Bernoulli** distribution for the *control* group.

$$
X \sim \mathcal{Bernoulli}(p)
$$

where p is the conversion probability of the control group.

According to the properties of the Bernoulli distribution, the mean and variance are as follows:

$$
E(X)=p \\
Var(X)=p(1-p)
$$

According to the **central limit theorem**, by calculating many **sample means** we can approximate the true mean of the population, $𝜇$, from which the data for the control group was taken. The distribution of the sample means, $p$, will be normally distributed around the true mean with a standard deviation equal to the **standard error of the mean**. The equation for this is given as:
$$
\sigma_{\bar{x}}=\frac{s}{\sqrt{n}} = \sqrt{\frac{p(1-p)}{n}}
$$

Therefore, we can represent both groups as a normal distribution with the following properties:
$$
\hat{p} \sim \mathcal{N}(p,\sqrt{\frac{p(1-p)}{n}})
$$

The same can be done for the test group. So, we will have two normal distributions for $p_A$ and $p_B$


```python
# standard error of the mean for both groups

SE_A=np.sqrt(p_A*(1-p_A)/A_total)
SE_B=np.sqrt(p_B*(1-p_B)/B_total)

# plot the null and alternative hypothesis
fig, ax = plt.subplots(figsize=(12,6))

x = np.linspace(0, .2, 1000)
yA = scs.norm(p_A, SE_A).pdf(x)
ax.plot(x, yA)
ax.axvline(x=p_A, c='red', alpha=0.5, linestyle='--')
yB = scs.norm(p_B, SE_B).pdf(x)
ax.plot(x, yB)
ax.axvline(x=p_B, c='blue', alpha=0.5, linestyle='--')
plt.xlabel('Converted Proportion')
plt.ylabel('PDF')
```




    Text(0, 0.5, 'PDF')




![png](Math behind_17_1.png)


The dashed lines represent the mean conversion rate for each group. The distance between the red dashed line and the blue dashed line is equal to mean difference between the control and test group. $\hat{d}$ is the distribution of the difference between random variables from the two groups.
$$
\hat{d}=\hat{p_B}-\hat{p_A}
$$

### Variance of the Sum

Recall that the null hypothesis states that the **difference in probability** between the two groups is $0$. Therefore, the *mean* for this normal distribution will be at $0$. 

A basic property of variance is that the variance of the sum of two random independent variables is the sum of the variances.

$$
Var(X-Y)=Var(X)+Var(Y)
$$

This means that the *null hypothesis* and *alternative hypothesis* will have the same variance which will be the sum of the variances for the control group and the test group.

$$
Var(\hat{d})=Var({\hat{p_B}-\hat{p_A}})=Var(\hat{p_B})+Var(\hat{p_A})=\frac{\hat{p_B}(1-\hat{p_B})}{n_B}+\frac{\hat{p_A}(1-\hat{p_A})}{n_A}
$$

The standard deviation can then be calculated as:

$$
SE=\sqrt{Var(\hat{d})}=\sqrt{\frac{s_A^2}{n_B}+\frac{s_B^2}{n_A}}
$$

and we get the ***Satterthwaite approximation*** for **pooled standard error**. If we calculate the **pooled probability** and use the pooled probability to calculate the standard deviation for both groups, we get:

$$
\sigma=\sqrt{Var(\hat{d})}=\sqrt{\frac{s_A^2}{n_B}+\frac{s_B^2}{n_A}}=\sqrt{\frac{s_{pool}^2}{n_B}+\frac{s_{pool}^2}{n_A}} = \sqrt{\hat{p}_{pool}(1-\hat{p}_{pool})\frac{1}{n_B}+\frac{1}{n_A}}
$$

where:
$$
\hat{p}_{pool}=\frac{p_An_A+p_Bn_B}{n_B+n_A}
$$

### Compare the Null Hypothesis vs. the Alternative Hypothesis 

**null hypothesis** : the change in the design made for the test group would **result in no change** in the conversion rate.

**alternative hypothesis** : the change in the design for the test group would **result in an improvement** (or reduction) in the conversion rate.

The **null hypothesis** will be a *normal distribution* with a mean of zero and a standard deviation equal to the pooled standard error.

$$
H_0: d=0 \\
\hat{d}_0 \sim \mathcal{N}(0,SE_{pool})
$$

The **alternative hypothesis** has the same standard deviation as the null hypothesis, but the *mean* will be located at the difference in the conversion rate, $\hat{d}$. This makes sense because we can calculate the difference in the conversion rates directly from the data, but the normal distribution represents the possible values our experiment could have given us.

$$
H_1: d=p_B-p_A \\
\hat{d}_1 \sim \mathcal{N}(d,SE_{pool})
$$


```python
def z_val(sig_level=0.05):
    """Returns the z value for a given significance level"""
    z_dist = scs.norm()
    area = 1 - sig_level/2
    return z_dist.ppf(area)

def confidence_interval(sample_mean=0, sample_std=1, sample_size=1,
                        sig_level=0.05):
    """Returns the confidence interval as a tuple"""
    z = z_val(sig_level)

    left = sample_mean - z * sample_std / np.sqrt(sample_size)
    right = sample_mean + z * sample_std / np.sqrt(sample_size)
    return (left, right)
```


```python
n = N_A + N_B
bcr = p_A  
d=0
d_hat = p_B - p_A

# define parameters to find pooled standard error
X_A = bcr * N_A
X_B = (bcr + d_hat) * N_B

# pooled probability for two samples
p_pool=(X_A + X_B) / (N_A + N_B)
SE_pool = np.sqrt(p_pool * (1 - p_pool) * (1 / N_A + 1 / N_B))

# null hypothesis: d ~ N(0,SE_pool)
d=0
x = np.linspace(d - 12 * SE_pool, d + 12 * SE_pool, 1000)
y = scs.norm(d, SE_pool).pdf(x)
# null hypothsis's confidence interval
left, right = confidence_interval(sample_mean=d, sample_std=SE_pool,
                                  sig_level=0.05)


# althernative hypothsis: d ~ N(p_B-p_A,SE_pool)
x_1 = np.linspace(d_hat - 12 * SE_pool, d_hat + 12 * SE_pool, 1000)
y_1 = scs.norm(d_hat, SE_pool).pdf(x_1)

# althernative hypothsis's confidence interval
left_1, right_1 = confidence_interval(sample_mean=d_hat, sample_std=SE_pool,
                                      sig_level=0.05)
alternative = scs.norm(d_hat, SE_pool)
null=scs.norm(d, SE_pool)
# plot
fig, ax = plt.subplots(figsize=(12, 6))
ax.plot(x, y, c='blue',label="Null")
ax.axvline(left, c='blue', linestyle='--', alpha=0.5)
ax.axvline(right, c='blue', linestyle='--', alpha=0.5)
x_=np.linspace(-12 * SE_pool, 12 * SE_pool,1000)
ax.fill_between(x_, 0, alternative.pdf(x_), color='green', alpha=0.25, \
                where=(x_ > right))
ax.fill_between(x_, 0, alternative.pdf(x_), color='pink', alpha=0.25, \
                where=(x_ <= right))
ax.fill_between(x_, 0, null.pdf(x_), color='black', alpha=0.35, \
                where=(x_ > right))
ax.text(5 * SE_pool, null.pdf(0),
                'power = {0:.3f}'.format(1 - alternative.cdf(right)),
                fontsize=12, ha='right', color='k')
ax.text(-3 * SE_pool, null.pdf(0),
                'alpha right = {0:.3f}'.format(1 - null.cdf(right)),
                fontsize=12, ha='right', color='k')
ax.plot(x_1, y_1, c='red',label="Alternative")
# ax.axvline(left_1, c='red', linestyle='--', alpha=0.5)
# ax.axvline(right_1, c='red', linestyle='--', alpha=0.5)

ax.set_xlim(-8 * SE_pool, 8 * SE_pool)
plt.legend()
plt.xlabel('d')
plt.ylabel('PDF')
plt.show()
```


![png](Math behind_22_0.png)


Visually, the plot for the null and alternative hypothesis looks very similar to the other plots above. Fortunately, both curves are identical in shape, so we can just compare the distance between the means of the two distributions. We can see that the alternative hypothesis curve suggests that the test group has a higher conversion rate than the control group. This plot also can be used to directly determine the statistical power. 

## Statistical Power and Significance Level

The green shaded area represents the **statistical power**, calculated by finding the ***area under the alternative hypothesis distribution and outside of the confidence interval of the null hypothesis***.

After running our experiment, we get a resulting conversion rate for both groups. If we calculate the difference between the conversion rates, we end up with one result, the difference or the effect of the design change. Our task is to determine which population this result came from, the null hypothesis or the alternative hypothesis.

If the alternative design is truly better, the ***power*** is the probability that we *accept the alternative hypothesis and reject the null hypothesis* and is equal to the area shaded green (***true positive***).

The opposite area under the alternative curve is the probability that we *accept the null hypothesis and reject the alternative hypothesis* (***false negative***). This is referred to as $\beta$ in A/B testing or pink shaded area. 

If the null hypothesis is true and there truly is **no difference** between the control and test groups, then the *significance level* $= \alpha$ is the probability that we would *reject the null hypothesis and accept the alternative hypothesis* (**false positive**). A **false positive** is when we mistakenly conclude that the new design is better. This value is low because we want to limit this probability.

A typical 95% confidence level for an A/B test corresponds to a significance level of 0.05.
$$
\alpha=1-\text{confidence interval}
$$

![](confusion.png)


Experiments are typically set up for a *minimum desired power* of 80%. If our new design is truly better, we want our experiment to show that there is at least an 80% probability that this is the case. Unfortunately, our current experiment only has a power of 0.419. 

We know that if we **increase the sample size** for each group, we will *decrease the pooled variance* for our null and alternative hypothesis. This will *make our distributions much narrower* and may *increase the statistical power*. Let’s take a look at how sample size will directly affect our results.

## Sample Size 

You will need the **baseline conversion rate (bcr)** and the **minimum detectable effect**, which is the minimum difference between the control and test group that you or your team will determine to be worth the investment of making the design change in the first place.

**Equation for minimum sample size**:
$$
n=\frac{2p_{pool}(1-p_{pool})(z_{\beta}+z_{\alpha/2})^2}{(p_B-p_A)^2}
$$

![](sample_size.png)


**Derivation of sample size formula**:

$$
\begin{align}
&\text{critical value}=\mu+z_{\alpha/2}*SE \\
\text{Thus  }\quad &0+z_{\alpha/2}*SE=\hat{d}-SE \cdot z_{power} \\
\text{We can get  }\quad &z_{power} = \frac{ \hat{d}-z_{\alpha/2} \cdot SE}{SE} \\
\text{As  }\quad &SE=\sqrt{\frac{\sigma^2}{n_1}+\frac{\sigma^2}{n_2}} \\ 
\text{If ratio $r=\frac{n_2}{n_1}$  }\quad &SE=\sqrt{\frac{\sigma^2}{n_1}+\frac{\sigma^2}{rn_2}} \\ 
&z_{power} = \frac{\hat{d}}{\sqrt{\frac{\sigma^2}{n_1}+\frac{\sigma^2}{rn_2}}} -z_{\alpha/2} \\
&z_{power} =\frac{\hat{d}}{\sqrt{\frac{(r+1)\sigma^2}{rn_1}}} -z_{\alpha/2} \\
&(r+1)\sigma^2(z_{power}+z_{\alpha/2})^2 =rn_1\hat{d}^2 \\
&n_1=\frac{r+1}{r}\frac{\sigma^2 (z_{power}+z_{\alpha/2})^2 }{\hat{d}^2} \\
\text{If r=1(equal groups)  } &n_1=\frac{2\sigma^2 (z_{power}+z_{\alpha/2})^2 }{\hat{d}^2} \\
\end{align}
$$


#### Sample Size Formula for difference in means

$$
n_1=\frac{r+1}{r}\frac{\sigma^2 (z_{power}+z_{\alpha/2})^2 }{\hat{d}^2}
$$

where
$$
\begin{align}
n1 &= \text{size of smaller group} \\
r &= \text{ratio of larger group to smaller group} \\
\sigma &= \text{SD of the charateristic} \\
difference &= \text{meaningful difference in means of 2 groups} \\
z_{power} &= \text{corresponds to power (0.84 for power = 0.8)} \\
z_{\alpha/2}&= \text{corresponds to 2-tailed significance level (1.96 for $\alpha$=0.05)} \\
\end{align}
$$


```python
power=0.8
sig_level=0.05
mde=0.02
bcr=0.1
# standard normal distribution to determine z-values
standard_norm = scs.norm(0, 1)

# find Z_beta from desired power
Z_beta = standard_norm.ppf(power)

# find Z_alpha
Z_alpha = standard_norm.ppf(1-sig_level/2)

# average of probabilities from both groups
pooled_prob = (bcr + bcr+mde) / 2

min_N = (2 * pooled_prob * (1 - pooled_prob) * (Z_beta + Z_alpha)**2
         / mde**2)

print("Minimum sample size:",int(min_N))
```

    Minimum sample size: 3842









# Reference

[The pitfalls of A/B testing in social networks](https://tech.okcupid.com/the-pitfalls-of-a-b-testing-in-social-networks/)

[5 Validity Threats That Will Make Your A/B Tests Useless](https://splitbase.com/blog/ab-testing-threats)

[The Math Behind A/B Testing with Example Python Code](https://towardsdatascience.com/the-math-behind-a-b-testing-with-example-code-part-1-of-2-7be752e1d06f)

[The Power of Bayesian A/B Testing](https://medium.com/convoy-tech/the-power-of-bayesian-a-b-testing-f859d2219d5)

