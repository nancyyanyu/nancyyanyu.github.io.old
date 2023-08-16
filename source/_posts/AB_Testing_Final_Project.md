---
title: A/B Testing Final Project
categories: Product Sense
mathjax: true
tags: ab-testing
comments: true
abbrlink: 8fdfc10f
date: 2020/07/03 14:28:54
---



Implementation of Udacity A/B Testing course final project. 

<!--more-->



# Experiment Overview: Free Trial Screener

At the time of this experiment, Udacity courses currently have two options on the course overview page: "start free trial", and "access course materials". If the student clicks "start free trial", they will be asked to enter their credit card information, and then they will be enrolled in a free trial for the paid version of the course. After 14 days, they will automatically be charged unless they cancel first. If the student clicks "access course materials", they will be able to view the videos and take the quizzes for free, but they will not receive coaching support or a verified certificate, and they will not submit their final project for feedback.


In the experiment, Udacity tested a change where if the student clicked "start free trial", they were asked how much time they had available to devote to the course. If the student indicated 5 or more hours per week, they would be taken through the checkout process as usual. If they indicated fewer than 5 hours per week, a message would appear indicating that Udacity courses usually require a greater time commitment for successful completion, and suggesting that the student might like to access the course materials for free. At this point, the student would have the option to continue enrolling in the free trial, or access the course materials for free instead. This screenshot shows what the experiment looks like.


The **hypothesis** was that this might set clearer expectations for students upfront, thus reducing the number of frustrated students who left the free trial because they didn't have enough time—without significantly reducing the number of students to continue past the free trial and eventually complete the course. If this hypothesis held true, Udacity could improve the overall student experience and improve coaches' capacity to support students who are likely to complete the course.


The **unit of diversion is a cookie**, although if the student enrolls in the free trial, they are tracked by user-id from that point forward. The same user-id cannot enroll in the free trial twice. For users that do not enroll, their user-id is not tracked in the experiment, even if they were signed in when they visited the course overview page.

## Metric Choice
Which of the following metrics would you choose to measure for this experiment and why? For each metric you choose, indicate whether you would use it as an invariant metric or an evaluation metric. The practical significance boundary for each metric, that is, the difference that would have to be observed before that was a meaningful change for the business, is given in parentheses. All practical significance boundaries are given as absolute changes.


Any place "unique cookies" are mentioned, the uniqueness is determined by day. (That is, the same cookie visiting on different days would be counted twice.) User-ids are automatically unique since the site does not allow the same user-id to enroll twice.


- ***Number of cookies***: That is, number of unique cookies to view the course overview page. ($d_{min}=3000$)
- ***Number of user-ids***: That is, number of users who enroll in the free trial. ($d_{min}=50$)
- ***Number of clicks***: That is, number of unique cookies to click the "Start free trial" button (which happens before the free trial screener is trigger). ($d_{min}=240$)
- ***Click-through-probability***: That is, number of unique cookies to click the "Start free trial" button divided by number of unique cookies to view the course overview page. ($d_{min}=0.01$)
- ***Gross conversion***: That is, number of user-ids to complete checkout and enroll in the free trial divided by number of unique cookies to click the "Start free trial" button. ($d_{min}=0.01$)
- ***Retention***: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by number of user-ids to complete checkout. ($d_{min}=0.01$)
- ***Net conversion***: That is, number of user-ids to remain enrolled past the 14-day boundary (and thus make at least one payment) divided by the number of unique cookies to click the "Start free trial" button. ($d_{min}=0.0075$)

You should also decide now what results you will be looking for in order to launch the experiment. Would a change in any one of your evaluation metrics be sufficient? Would you want to see multiple metrics all move or not move at the same time in order to launch? This decision will inform your choices while designing the experiment.



***Invariant Metrics*** : number of cookies, number of clicks, click-through-probability.

***Evaluation Metrics*** : gross conversion, retention, net conversion.

## Measure Variability


```python
import math
n_pageviews=40000
n_clicks=3200
n_enroll=660
ctp=0.08
n_sample=5000

click_through_probability=0.08 #clicks / pageviews
gross_conversion=0.20625 # enroll / click
retention=0.53 # payment / enroll
net_conversion=0.1093125 # payment / click
```


```python
"""analytic standard deviation estimate"""
# gross_conversion
std_gross_conversion=math.sqrt(gross_conversion*(1-gross_conversion)/(n_clicks/n_pageviews*n_sample))
# retention
std_retention=math.sqrt(retention*(1-retention)/(n_enroll/n_pageviews*n_sample))
# net_conversion
std_net_conversion=math.sqrt(net_conversion*(1-net_conversion)/(n_clicks/n_pageviews*n_sample))
print("SD of GC: ",round(std_gross_conversion,4))
print("SD of Retention: ",round(std_retention,4))
print("SD of NC: ",round(std_net_conversion,4))
```

    SD of GC:  0.0202
    SD of Retention:  0.0549
    SD of NC:  0.0156


## Sizing

- Will you use Bonferroni Correction?
> Evaluation metrics are closely related to each other, so that Bonferroni would be too conservative.

- Which evaluation metrics did you choose?
> gross conversion, retention, net conversion

- How many pageviews will you need?

### Using sample size calculator
https://www.evanmiller.org/ab-testing/sample-size.html

**gross conversion**
- Baseline rate: 20.625% 
- Minimum Detectable Effect: 0.01
- Sample size: 25,835 clicks/group
- Total sample size: 25,835*2=51670 clicks
- Pageviews= 51670 / 0.08 (clicks / pageviews)=645875

**retention**
- Baseline rate: 53% 
- Minimum Detectable Effect: 0.01
- Sample size: 39,115 enrolls/group
- Total sample size: 39,115*2=78230 enrolls
- Pageviews= 78230 / (660/40000) (enrolls / pageviews)=4741212

**net conversion**
- Baseline rate: 10.93125% 
- Minimum Detectable Effect: 0.0075
- Sample size: 27,413 clicks/group
- Total sample size: 27,413*2=54826 clicks
- Pageviews= 54826 / 0.08 (clicks / pageviews)=685325

The maximum number of pageviews is 4741212

### Using Standard Error


```python
from scipy.stats import norm
# Inputs:
#   The desired alpha for a two-tailed test
# Returns: The z-critical value
def get_z_star(alpha):
    return norm.ppf((1-alpha/2))

# Inputs:
#   z-star: The z-critical value
#   s: The standard error of the metric at N=1
#   d_min: The practical significance level
#   N: The sample size of each group of the experiment
# Returns: The beta value of the two-tailed test
def get_beta(z_star,s, d_min, N):
    SE = s /  math.sqrt(N)
    return norm.cdf(z_star*SE,d_min,SE)

# Inputs:
#   s: The standard error of the metric with N=1 in each group
#   d_min: The practical significance level
#   Ns: The sample sizes to try
#   alpha: The desired alpha level of the test
#   beta: The desired beta level of the test
# Returns: The smallest N out of the given Ns that will achieve the desired
#          beta. There should be at least N samples in each group of the experiment.
#          If none of the given Ns will work, returns -1. N is the number of
#          samples in each group.
def required_size(s, d_min, Ns=200000, alpha=0.05, beta=0.2):
    z=get_z_star(alpha)
    for N in range(1,Ns):        
        if get_beta(z, s, d_min, N) <= beta:
            return N
    return -1
```


```python
# s is the pooled standard error for N=1 in each group,
"""number of clicks needed for gross_conversion"""
n=1
s=math.sqrt(gross_conversion*(1-gross_conversion)/n)
d_min=0.01
req=required_size(s,d_min)
print("number of clicks needed for gross_conversion:",int(req))
print("number of pageviews needed for gross_conversion:",int(req/click_through_probability))
```

    number of clicks needed for gross_conversion: 12850
    number of pageviews needed for gross_conversion: 160625



```python
"""number of enrollment needed for retention"""
n=1
s=math.sqrt(retention*(1-retention)/n)
d_min=0.01
req=required_size(s,d_min)
print("number of clicks needed for retention:",int(req))
print("number of pageviews needed for retentionn:",int(req/(n_enroll/n_pageviews)))
```

    number of clicks needed for retention: 19552
    number of pageviews needed for retentionn: 1184969



```python
"""number of clickes needed for net_conversion"""
n=1
s=math.sqrt(net_conversion*(1-net_conversion)/n)
d_min=0.0075
req=required_size(s,d_min)
print("number of clicks needed for net_conversion:",int(req))
print("number of pageviews needed for net_conversion:",int(req/click_through_probability))
```

    number of clicks needed for net_conversion: 13586
    number of pageviews needed for net_conversion: 169825


## Duration and Exposure


```python
fraction_of_traffic=1
duration=685325/(n_pageviews*fraction_of_traffic)
```


```python
duration
```




    17.133125



## Sanity Check
For each invariant metric, compute a 95% confidence interval for the value you expect to observe.

For invariant metrics we expect equal diversion into the experiment and control group.


```python
dates=['Sat, Oct 11', 'Sun, Oct 12', 'Mon, Oct 13', 'Tue, Oct 14',
       'Wed, Oct 15', 'Thu, Oct 16', 'Fri, Oct 17', 'Sat, Oct 18',
       'Sun, Oct 19', 'Mon, Oct 20', 'Tue, Oct 21', 'Wed, Oct 22',
       'Thu, Oct 23', 'Fri, Oct 24', 'Sat, Oct 25', 'Sun, Oct 26',
       'Mon, Oct 27', 'Tue, Oct 28', 'Wed, Oct 29', 'Thu, Oct 30',
       'Fri, Oct 31', 'Sat, Nov 1', 'Sun, Nov 2', 'Mon, Nov 3',
       'Tue, Nov 4', 'Wed, Nov 5', 'Thu, Nov 6', 'Fri, Nov 7',
       'Sat, Nov 8', 'Sun, Nov 9', 'Mon, Nov 10', 'Tue, Nov 11',
       'Wed, Nov 12', 'Thu, Nov 13', 'Fri, Nov 14', 'Sat, Nov 15',
       'Sun, Nov 16']
pageviews_cont=[ 7723,  9102, 10511,  9871, 10014,  9670,  9008,  7434,  8459,
       10667, 10660,  9947,  8324,  9434,  8687,  8896,  9535,  9363,
        9327,  9345,  8890,  8460,  8836,  9437,  9420,  9570,  9921,
        9424,  9010,  9656, 10419,  9880, 10134,  9717,  9192,  8630,
        8970]
pageviews_exp=[ 7716,  9288, 10480,  9867,  9793,  9500,  9088,  7664,  8434,
       10496, 10551,  9737,  8176,  9402,  8669,  8881,  9655,  9396,
        9262,  9308,  8715,  8448,  8836,  9359,  9427,  9633,  9842,
        9272,  8969,  9697, 10445,  9931, 10042,  9721,  9304,  8668,
        8988]
clicks_cont=[687, 779, 909, 836, 837, 823, 748, 632, 691, 861, 867, 838, 665,
       673, 691, 708, 759, 736, 739, 734, 706, 681, 693, 788, 781, 805,
       830, 781, 756, 825, 874, 830, 801, 814, 735, 743, 722]
clicks_exp=[686, 785, 884, 827, 832, 788, 780, 652, 697, 860, 864, 801, 642,
       697, 669, 693, 771, 736, 727, 728, 722, 695, 724, 789, 743, 808,
       831, 767, 760, 850, 851, 831, 802, 829, 770, 724, 710]
enrolls_cont=[134, 147, 167, 156, 163, 138, 146, 110, 131, 165, 196, 162, 127,
       220, 176, 161, 233, 154, 196, 167, 174, 156, 206]
enrolls_exp=[105, 116, 145, 138, 140, 129, 127,  94, 120, 153, 143, 128, 122,
       194, 127, 153, 213, 162, 201, 207, 182, 142, 182]
payment_cont=[ 70,  70,  95, 105,  64,  82,  76,  70,  60,  97, 105,  92,  56,
       122, 128, 104, 124,  91,  86,  75, 101,  93,  67]
payment_exp=[ 34,  91,  79,  92,  94,  61,  44,  62,  77,  98,  71,  70,  68,
        94,  81, 101, 119, 120,  96,  67, 123, 100, 103]
ctp_cont=[i/j for i,j in zip(clicks_cont,pageviews_cont)]
ctp_exp=[i/j for i,j in zip(clicks_exp,pageviews_exp)]
```


```python
"""pageviews"""
sum_pv_cont=sum(pageviews_cont)
sum_pv_exp=sum(pageviews_exp)
SD_pageviews=math.sqrt(0.5*0.5/(sum_pv_cont+sum_pv_exp))
m=1.96*SD_pageviews
ci_min,ci_max=0.5-m,0.5+m
print("Confidence Interval for pageviews: [{},{}]".format(round(ci_min,4),round(ci_max,4)))
print("Observed: ",round(sum_pv_cont/(sum_pv_exp+sum_pv_cont),4))
```

    Confidence Interval for pageviews: [0.4988,0.5012]
    Observed:  0.5006



```python
"""clicks"""
sum_click_cont=sum(clicks_cont)
sum_click_exp=sum(clicks_exp)
SD_clicks=math.sqrt(0.5*0.5/(sum_click_cont+sum_click_exp))
m=1.96*SD_clicks
ci_min,ci_max=0.5-m,0.5+m
print("Confidence Interval for clicks: [{},{}]".format(round(ci_min,4),round(ci_max,4)))
print("Observed: ",round(sum_click_cont/(sum_click_exp+sum_click_cont),4))
```

    Confidence Interval for clicks: [0.4959,0.5041]
    Observed:  0.5005



```python
"""click_through_probability"""
ctp_cont=sum_click_cont/sum_pv_cont
ctp_exp=sum_click_exp/sum_pv_exp
d_hat=ctp_exp-ctp_cont
ctp_pool=(sum_click_cont+sum_click_exp)/(sum_pv_cont+sum_pv_exp)
SE_ctp=math.sqrt(ctp_pool*(1-ctp_pool)*(1/sum_pv_cont+1/sum_pv_exp))
m=1.96*SE_ctp
ci_min,ci_max=-m,m
print("Confidence Interval for ctp: [{},{}]".format(round(ci_min,4),round(ci_max,4)))
print("Observed: ",round(d_hat,4))
```

    Confidence Interval for ctp: [-0.0013,0.0013]
    Observed:  0.0001


## Effective Size Test
For each evaluation metric, compute a 95% confidence interval around the difference between the experiment and control group


```python
"""gross conversion"""
n=len(enrolls_exp)
d_min=0.01
sum_clicks_cont=sum(clicks_cont[:n])
sum_clicks_exp=sum(clicks_exp[:n])
sum_enroll_cont=sum(enrolls_cont[:n])
sum_enroll_exp=sum(enrolls_exp[:n])
p_pool=(sum_enroll_exp+sum_enroll_cont)/(sum_clicks_exp+sum_clicks_cont)
SE_pool=math.sqrt(p_pool*(1-p_pool)*(1/sum_clicks_cont+1/sum_clicks_exp))
m=SE_pool*1.96
d_hat=sum_enroll_exp/sum_clicks_exp-sum_enroll_cont/sum_clicks_cont
print("Confidence Interval:[{},{}]".format(d_hat-m,d_hat+m))
print("Observed:",d_hat)
print ("Statistically significant:", d_hat+m<0 or d_hat-m>0 ,",  CI doesn't include 0")
print("Practically significant:",True,",  CI doesn't include d_min or -d_min")
```

    Confidence Interval:[-0.0291233583354044,-0.01198639082531873]
    Observed: -0.020554874580361565
    Statistically significant: True ,  CI doesn't include 0
    Practically significant: True ,  CI doesn't include d_min or -d_min



```python
"""retention"""
n=len(payment_exp)
d_min=0.01
sum_payment_cont=sum(payment_cont[:n])
sum_payment_exp=sum(payment_exp[:n])
sum_enroll_cont=sum(enrolls_cont[:n])
sum_enroll_exp=sum(enrolls_exp[:n])
p_pool=(sum_payment_cont+sum_payment_exp)/(sum_enroll_cont+sum_enroll_exp)
SE_pool=math.sqrt(p_pool*(1-p_pool)*(1/sum_enroll_cont+1/sum_enroll_exp))
m=SE_pool*1.96
d_hat=sum_payment_exp/sum_enroll_exp-sum_payment_cont/sum_enroll_cont
print("Confidence Interval:[{},{}]".format(d_hat-m,d_hat+m))
print("Observed:",d_hat)
print ("Statistically significant:", d_hat+m<0 or d_hat-m>0 ,",  CI doesn't include 0")
print("Practically significant:",False,",  CI include d_min")
```

    Confidence Interval:[0.008104435728019967,0.05408517368626556]
    Observed: 0.031094804707142765
    Statistically significant: True ,  CI doesn't include 0
    Practically significant: False ,  CI include d_min



```python
"""net conversion"""
n=len(enrolls_exp)
d_min=0.0075
sum_clicks_cont=sum(clicks_cont[:n])
sum_clicks_exp=sum(clicks_exp[:n])
sum_payment_cont=sum(payment_cont[:n])
sum_payment_exp=sum(payment_exp[:n])
p_pool=(sum_payment_exp+sum_payment_cont)/(sum_clicks_exp+sum_clicks_cont)
SE_pool=math.sqrt(p_pool*(1-p_pool)*(1/sum_clicks_cont+1/sum_clicks_exp))
m=SE_pool*1.96
d_hat=sum_payment_exp/sum_clicks_exp-sum_payment_cont/sum_clicks_cont
print("Confidence Interval:[{},{}]".format(d_hat-m,d_hat+m))
print("Observed:",d_hat)
print ("Statistically significant:", d_hat+m<0 or d_hat-m>0 ,",  CI doesn't include 0")
print("Practically significant:",False,",  CI include d_min")
```

    Confidence Interval:[-0.011604624359891718,0.001857179010803383]
    Observed: -0.0048737226745441675
    Statistically significant: False ,  CI doesn't include 0
    Practically significant: False ,  CI include d_min


## Sign Tests
Run a sign test on each evaluation metric using the day-by-day data


```python
from scipy.stats import binom_test 
"""gross conversion"""
alpha=0.05
beta=0.2

gc_exp=[i/j for i,j in zip(enrolls_exp,clicks_exp)]
gc_cont=[i/j for i,j in zip(enrolls_cont,clicks_cont)]
gc_diff=sum([i>j for i,j in zip(gc_exp,gc_cont)])
days=len(gc_exp)

# The prob of gross conversion of experiment group > gross conversion of control group is 0.5
p_value=binom_test(gc_diff, n=days, p=0.5)
print("p-value:",p_value,", Statistically Significant:",p_value<alpha)
```

    p-value: 0.0025994777679443364 , Statistically Significant: True



```python
"""retention"""
rt_exp=[i/j for i,j in zip(payment_exp,enrolls_exp)]
rt_cont=[i/j for i,j in zip(payment_cont,enrolls_cont)]
rt_diff=sum([i>j for i,j in zip(rt_exp,rt_cont)])
days=len(rt_exp)
p_value=binom_test(rt_diff, n=days, p=0.5)
print("p-value:",p_value,", Statistically Significant:",p_value<alpha)
```

    p-value: 0.6776394844055175 , Statistically Significant: False



```python
len(gc_exp)
```




    23




```python
"""net conversion"""
nc_exp=[i/j for i,j in zip(payment_exp,clicks_exp)]
nc_cont=[i/j for i,j in zip(payment_cont,clicks_cont)]
nc_diff=sum([i>j for i,j in zip(nc_exp,nc_cont)])
days=len(nc_exp)
p_value=binom_test(nc_diff, n=days, p=0.5)
print("p-value:",p_value,", Statistically Significant:",p_value<alpha)
```

    p-value: 0.6776394844055175 , Statistically Significant: False


>Recomendation: not to launch
