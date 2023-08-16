---
title: 'Analysis of 2018 H-1B Sponsorship for Data Science Employees'
categories: Project
mathjax: true
comments: true
abbrlink: 8b70757d
layout: post
date: 2019/10/19
hidden: true

---



# Introduction

The **H-1B** is a visa in the U.S. that allows U.S. employers to temporarily employ foreign workers in specialty occupations. For international students who are trying to find Data science jobs in the U.S., H-1B visa is the most common working visa. Job hunting is stressful, so the tatics show more importance when selecting the companies to apply. Not saying there are no chances in companies with no H-1B sponsor records in 2018, as policies vary in each company every year. Accessing more information and prioritizing tasks in hand are what new grads need to do.



I want to dig into the data of H-1B case disclosure file in 2018 a little. The data could be found at U.S. Department of Labor's website: https://www.foreignlaborcert.doleta.gov/performancedata.cfm#dis. I would just focus on data related entry level jobs.



The ***Jupyter notebook*** is [here](https://github.com/nancyyanyu/mini_projects/blob/master/h1b_analysis/h1b_analysis.ipynb).

<!--more-->





# Key Insights

#### Companies in California, New Jersey, Texas, New York, Illinois like to sponsor data science employees most, which takes up >50% of that in the U.S. 

```python
#count number of data related job H-1B cases in each state
data_geo=df[df.JOB_TITLE.isin(data_job_title)].groupby('EMPLOYER_STATE')['CASE_NUMBER'].count().sort_values(ascending=False).to_frame()
data_geo.to_csv('./data_geo.csv')
```



For any job titles containing *Data*, *Machine Learning*, *Research Scientist*, *Applied Scientist*, *SQL*, I would include them as data science related jobs. I know there are job titles like *Product Analyst* which are essentially *Data Analyst* or *Data Scientist*, but I'll ignore these cases here. 

We can see the top 5 states sponsor $9699$ data science employees which take up more than $50\%$ of that in the U.S. 



Here is a map in Tableau that demonstrates how many data science employees sponsored in each state.

<iframe src="https://public.tableau.com/shared/MCQ8F254G?:showVizHome=no&:embed=true"  width="900" height="600"></iframe>


#### Besides big tech companies, ICCs sponsor a LOT!

```python
data_company=data_company.groupby('EMPLOYER_NAME')['CASE_NUMBER'].sum().sort_values(ascending=False).to_frame()
```

Here is a visualization of companies who sponsor H-1B (Only shows companies with ≥ 20 cases)

<iframe src="https://public.tableau.com/views/Book4_15618711097530/NumberofDataScienceEmployeesSponsoredinEachCompany?:showVizHome=no&:embed=true"  width="900" height="450"></iframe>


#### More than 140 companies in Washington state sponsor, and they locate not only in Washington state

Since I'm only interested in companies located near Seattle, I extract the list of companies based in Washington state. Here is a visualization showing that for employers in Wahington state, 

- how many data science employees sponsered by each of these companies,
- how many cases in each job titles, and 
- how many data science employees sponsored by these companies across the nation (not just in Washington state).



There are several job hunting insights from these data. 

- There are 144 companies in Washington state that sponsor data science employees. Most of these companies only sponsor 1 or 2 data science employees. These companies are mostly small-size companies which are usually ignored by job hunters.
- Job titles like *Data Scientist*, *Applied Scientist*, *Research Scientist* are sponsored most. These job titles mostly required more than 5 years of professional experience or PhD degree. That's because these job titles mostly come from Microsoft and Amazon, and they rarely hire entry level people for data science.
- Companies based in Washington state not only sponsored people in the state, but also in other states, like California, Texas, New Jersey, and etc.

```python
data_comp=df[(df.EMPLOYER_STATE=='WA') & (df.JOB_TITLE.isin(data_job_title))].groupby('EMPLOYER_NAME')['CASE_NUMBER'].count().sort_values(ascending=False).reset_index()
data_comp_wa=pd.merge(data_comp,df[['EMPLOYER_CITY','EMPLOYER_STATE','EMPLOYER_NAME']].drop_duplicates(),on=['EMPLOYER_NAME'],how='left')
```



<iframe src="https://public.tableau.com/shared/82398BJSX?:showVizHome=no&:embed=true"  width="1000" height="800"></iframe>






**Ref:**

https://en.wikipedia.org/wiki/H-1B_visa

[U.S. Department of Labor]( https://www.foreignlaborcert.doleta.gov/performancedata.cfm#dis)

