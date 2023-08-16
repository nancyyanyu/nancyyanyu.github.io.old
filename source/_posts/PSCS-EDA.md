---
title: 'Study Note: Exploratory Data Analysis '
categories: 
- Machine Learning
- Practical Statistics for DS
mathjax: true
tags:
  - EDA
  - Statistics
comments: true
abbrlink: c8b80037
date: 2020/04/29 04:58:33
---



Study note of the book *Practical Statistics for Data Scientists: 50+ Essential Concepts Using R and Python - Chapter 1 Exploratory Data Analysis* .

<!--more-->

# Estimates of Location

**Trimmed mean**: calculate by dropping a fixed number of sorted values at each end and then taking an average of the remaining values

- $\bar{x}= \frac{\sum_{i=p+1}^{n-p}x_{(i)}}{n-2p}$
- Eliminates the influence of extreme values

**Weighted mean**: calculate by multiplying each data value $x_i$ by a user-specified weight $w_i$ and dividing their sum by the sum of the weights. 

- $\bar{x}_w=\frac{\sum_{i=1}^nx_iw_i}{\sum_{i=1}^nw_i}$

- Motivations for using a weighted mean:
  - Some values are intrinsically more variable than others, and highly variable observations are given a lower weight. 
  - The data collected does not equally represent the different groups that we are interested in measuring. 

**Weighted median**: first sort the data, although each data value has an associated weight. Instead of the middle number, the weighted median is a value such that the sum of the weights is equal for the lower and upper halves of the sorted list.



## *Robust* estimate of location

**median**: a *robust* estimate of location since it is not influenced by *outliers* (extreme cases) that could skew the results. 

**trimmed mean**: widely used to avoid the influence of outliers. 

-  A compromise between the median and the mean: it is robust to extreme values in the data, but uses more data to calculate the estimate for location.



# Estimates of Variability

***Variability***, also referred to as *dispersion*, measures whether the data values are tightly clustered or spread out.

**Mean absolute deviation**: $\frac{\sum_{i=1}^n|x_i-\bar{x}|}{n}$, where $x_i$ is the sample mean.

**Variance**: $s^2=\frac{\sum_{i=1}^n(x_i-\bar{x})^2}{n-1}$

> If you use the intuitive denominator of *n* in the variance formula, you will underestimate the true value of the variance and the standard deviation in the population. This is referred to as a *biased* estimate. However, if you divide by *n* – 1 instead of *n*, the variance becomes an *unbiased* estimate.
>
> To fully explain why using *n* leads to a biased estimate involves the notion of *degrees of freedom*, which takes into account the number of constraints in computing an estimate. In this case, there are *n* – 1 degrees of freedom since there is one constraint: the standard deviation depends on calculating the sample mean. 

**Standard deviation**: $s=\sqrt{variance}$

- SD is much easier to interpret than the variance since it is on the same scale as the original data. 
- SD is preferred in statistics over the mean absolute deviation b.o. working with squared values is much more convenient than absolute values, especially for statistical models.
- The variance and SD are especially sensitive to outliers since they are based on the squared deviations.



## *Robust* estimate of variability

***Median absolute deviation*** from the median (MAD): $Median(|x_1-m|,|x_2-m|,...,|x_N-m|)$, where *m* is the median.

- Sometimes, the *median absolute deviation* is multiplied by a constant scaling factor to put the MAD on the same scale as the SD in the case of a *normal distribution*. The commonly used factor of 1.4826 means that 50% of the normal distribution fall within the range $\pm MAD$



### **Estimates Based on Percentiles**

A different approach to estimating *dispersion* is based on looking at the spread of the *sorted data*. 

> *order statistics*

**Range**: (the most basic)  the difference between the largest and smallest numbers

- Extremely sensitive to outliers and not very useful as a general measure of dispersion in the data.

**Percentiles**: the *P*th percentile is a value such that at least *P* percent of the values take on this value or less and at least (100 – *P*) percent of the values take on this value or more. 

- e.g. - to find the 80th percentile, sort the data. Then, starting with the smallest value, proceed 80 percent of the way to the largest value. Note that the median is the same thing as the 50th percentile. 
- Essentially the same as a ***quantile***, with quantiles indexed by fractions (so the .8 quantile is the same as the 80th percentile).

**Interquartile range**(IQR):  the difference between the 25th percentile and the 75th percentile



# Exploring the Data Distribution

## **Percentiles and Boxplots**

***Quartiles*** (25th, 50th, and 75th percentiles) and the ***deciles*** (the 10th, 20th, ..., 90th percentiles). Percentiles are especially valuable for summarizing the ***tails*** (the outer range) of the distribution

<center><img src="./1.EDA_1.png" width="500" /></center>



***Boxplots***: based on percentiles and give a quick way to visualize the distribution of data. 

<center><img src="./1.EDA_2.png" width="300" /></center>

- From this boxplot we can immediately see that the median state population is about 5 million, half the states fall between about 2 million and about 7 million, and there are some high population outliers. 
- The top and bottom of the box are the 75th and 25th percentiles, respectively. The median is shown by the horizontal line in the box. The dashed lines, referred to as *whiskers*, extend from the top and bottom of the box to indicate the range for the bulk of the data.
- Any data outside of the whiskers is plotted as single points or circles (often considered outliers).



## Frequency Tables and Histograms

**Frequency table** of a variable divides up the variable range into *equally spaced* segments and tells us how many values fall within each segment.

The function `pandas.cut` creates a series that maps the values into the segments. Using the method `value_counts`, we get the frequency table:

```python
binnedPopulation = pd.cut(state['Population'], 10)
binnedPopulation.value_counts()
```



**Frequency tables v.s. Percentiles**

Both frequency tables and percentiles summarize the data by creating bins. In general, 

- quartiles and deciles will have the same count in each bin (equal-count bins), but the bin sizes will be different. 
- The frequency table will have different counts in the bins (equal-size bins), and the bin sizes will be the same.



**Histogram**: visualize a frequency table, with bins on the x- axis and the data count on the y-axis. 

- Empty bins are included in the graph. 
- Bins are of equal width.
- The number of bins (or, equivalently, bin size) is up to the user.
- Bars are contiguous—no empty space shows between bars, unless there is an empty bin.

<center><img src="./1.EDA_3.png" width="300" /></center>



**STATISTICAL MOMENTS**

In statistical theory, ***location*** and ***variability*** are referred to as the *first* and *second* ***moments*** of a distribution. The *third* and *fourth* moments are called ***skewness*** and ***kurtosis***. 

- **Skewness** refers to whether the data is skewed to larger or smaller values
- **Kurtosis** indicates the propensity of the data to have extreme values. 

Generally, metrics are not used to measure skewness and kurtosis; instead, these are discovered through visual displays.



## Density Plots and Estimates

**Density plot** can be thought of as a smoothed *histogram*, although it is typically computed directly from the data through a *kernel density estimate*.

- A key distinction from the histogram plotted in Figure 1-3 is the *scale of the y-axis*: a density plot corresponds to plotting the histogram as a ***proportion*** rather than ***counts***. Note that the total area under the density curve = 1, and instead of counts in bins you calculate areas under the curve between any two points on the x-axis, which correspond to the proportion of the distribution lying between those two points.

<center><img src="./1.EDA_4.png" width="300" /></center>



# Exploring Binary and Categorical Data

***Categorical data*** is typically summed up in proportions and can be visualized in a bar chart or pie chart.

- Categories might represent distinct things (apples and oranges, male and female), levels of a factor variable (low, medium, and high), or numeric data that has been binned.

**Mode**: the value—or values in case of a tie—that appears most often in the data - simple summary statistic for categorical data.

**Expected Value**: A special type of categorical data is data in which the categories represent or can be mapped to discrete values on the same scale.  This data can be summed up in a single “expected value,” which is a form of *weighted mean*, in which the weights are probabilities.

The expected value is calculated as follows:

1. Multiply each outcome by its probability of occurrence. 
2. Sum these values.



# **Correlation**

**Correlation coefficient**: gives an estimate of the correlation between two variables that always lies on the same scale. 

- ***Pearson’s correlation coefficient***: $r=\frac{\sum_{i=1}^n(x_i-\bar{x})(y_i-\bar{y})}{(n-1)s_xs_y}=\frac{\sum_{i=1}^n(x_i-\bar{x})(y_i-\bar{y})}{\sqrt{\sum(x_i-\bar{x})^2}\sqrt{\sum(y_i-\bar{y})^2}} \in [-1,+1]$ 
- Variables can have an association that is ***nonlinear***, in which case the correlation coefficient may not be a useful metric. 

**Correlation matrix**: A table where the variables are shown on both rows and columns, and the cell values are the **correlations** between the variables.

<center><img src="./1.EDA_5.png" width="500" /></center>



**Scatterplots**: The standard way to visualize the relationship between two measured data variables. The x-axis represents one variable and the y-axis another, and each point on the graph is a record.

<center><img src="./1.EDA_6.png" width="300" /></center>



# Exploring Two or More Variables

## Two Numeric  Variables

**Hexagonal binning**: A plot of two numeric variables with the records binned into hexagons.

```python
ax = kc_tax0.plot.hexbin(x='SqFtTotLiving',
y='TaxAssessedValue',
                         gridsize=30, sharex=False, figsize=
(5, 4))
ax.set_xlabel('Finished Square Feet')
ax.set_ylabel('Tax-Assessed Value')
```

<center><img src="./1.EDA_7.png" width="300" /></center>



**Contour plot**: A plot showing the density of two numeric variables like a topographical map. The contours are essentially a topographical map to two variables; each contour band represents a specific density of points, increasing as one nears a “peak.”

```python
ax = sns.kdeplot(kc_tax0.SqFtTotLiving,
kc_tax0.TaxAssessedValue, ax=ax)
ax.set_xlabel('Finished Square Feet')
ax.set_ylabel('Tax-Assessed Value')
```

<center><img src="./1.EDA_8.png" width="300" /></center>



## Two Categorical Variables

**Contingency table**: A tally of counts between two or more categorical variables.

<center><img src="./1.EDA_9.png" width="400" /></center>



## Categorical and Numeric Data

**Boxplots** : a simple way to visually compare the distributions of a numeric variable grouped according to a categorical variable.

<center><img src="./1.EDA_10.png" width="300" /></center>





**Violin plot**: Similar to a boxplot but plots the density estimate with the density on the y- axis.

- The advantage of a violin plot is that it can show nuances in the distribution that aren’t perceptible in a boxplot: the violin plot shows a concentration in the distribution near zero for Alaska and, to a lesser extent, Delta. This phenomenon is not as obvious in the boxplot.

```python
ax = sns.violinplot(airline_stats.airline,
airline_stats.pct_carrier_delay,
                    inner='quartile', color='white')
ax.set_ylabel('Daily % of Delayed Flights')
```

<center><img src="./1.EDA_11.png" width="300" /></center>



# Visualizing Multiple Variables

> The concept of *conditioning* variables  (in this case, zip code)

```python
zip_codes = [98188, 98105, 98108, 98126]
kc_tax_zip = kc_tax0.loc[kc_tax0.ZipCode.isin(zip_codes),:]

def hexbin(x, y, color, **kwargs):
	cmap = sns.light_palette(color, as_cmap=True) 
  plt.hexbin(x, y, gridsize=25, cmap=cmap, **kwargs)

g = sns.FacetGrid(kc_tax_zip, col='ZipCode', col_wrap=2)
g.map(hexbin, 'SqFtTotLiving', 'TaxAssessedValue',
      extent=[0, 3500, 0, 700000])
g.set_axis_labels('Finished Square Feet', 'Tax-Assessed
Value')
g.set_titles('Zip code {col_name:.0f}')
```

<center><img src="./1.EDA_12.png" width="300" /></center>