---
title: 'Spark SQL & DataFrame, SparkETL'
categories: Big Data
mathjax: true
tags:
  - Spark
  - ETL
  - SQL
comments: true
abbrlink: 9a328503
date: 2019-07-09 14:49:45
---

> Pyspark code of [Big Data Essentials: HDFS, MapReduce and Spark RDD](https://www.coursera.org/learn/big-data-essentials)


<!--more-->

# SQL and DataFrame



```python
import pyspark
from pyspark import SparkContext
from pyspark.sql import SparkSession
from pyspark.conf import SparkConf
```

A ***SparkSession*** can be used create DataFrame, register DataFrame as tables, execute SQL over tables, cache tables, and read parquet files. To create a SparkSession, use the following builder pattern:

***getOrCreate***: Gets an existing SparkSession or, if there is no existing one, creates a new one based on the options set in this builder


```python
conf=SparkConf().set("spark.python.profile", "true")
spark=SparkSession.builder.master("local").appName("wordcount").config(conf=SparkConf()).getOrCreate()
```

***createDataFrame***(data, schema=None, samplingRatio=None, verifySchema=True)

Creates a DataFrame from an RDD, a list or a pandas.DataFrame.




## Creates a DataFrame from a list


```python
l=[("alice",1),("bob",2)]
df=spark.createDataFrame(l,['name','age'])
df.collect()
```


    [Row(name='alice', age=1), Row(name='bob', age=2)]




```python
df.toPandas()
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
      <th>name</th>
      <th>age</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>alice</td>
      <td>1</td>
    </tr>
    <tr>
      <th>1</th>
      <td>bob</td>
      <td>2</td>
    </tr>
  </tbody>
</table>
</div>



## Creates a DataFrame from pandas DataFrame, and use sql query


```python
import pandas as pd
df2=spark.createDataFrame(pd.DataFrame(l,columns=['name','age']))
df2.collect()
```


    [Row(name='alice', age=1), Row(name='bob', age=2)]




```python
df2.select("name").collect()
```


    [Row(name='alice'), Row(name='bob')]




```python
df2.createOrReplaceTempView('table1')
spark.sql("select name from table1").collect()
```


    [Row(name='alice'), Row(name='bob')]




```python
spark.table("table1").collect()
```


    [Row(name='alice', age=1), Row(name='bob', age=2)]




```python
spark.stop()
```



## SQLContext

The entry point for working with structured data (rows and columns) in Spark, in Spark 1.x.

As of Spark 2.0, this is replaced by SparkSession. However, we are keeping the class here for backward compatibility.

A SQLContext can be used create DataFrame, register DataFrame as tables, execute SQL over tables, cache tables, and read parquet files.


```python
sc=SparkContext()
```


```python
from pyspark.sql import Row,SQLContext
sqlContext=SQLContext(sc)
rdd=sc.parallelize(l)
Person=Row("name","age")
person=rdd.map(lambda x: Person(*x))
sqlContext.createDataFrame(person).collect()
```


    [Row(name='alice', age=1), Row(name='bob', age=2)]




```python
sc.stop()
```



# SparkETL 

ETL is a type of data integration process referring to three distinct but interrelated steps (Extract, Transform and Load) and is used to synthesize data from multiple sources many times to build a Data Warehouse, Data Hub, or Data Lake.

Let's write an ETL job on pyspark!

Reference: (https://github.com/AlexIoannides/pyspark-example-project)

Before building ETL process, we write a function *start_spark* to start our sparkSession, update and get our configuration.




```python
import __main__

from os import environ,listdir,path
from pyspark import SparkFiles
from pyspark.sql import SparkSession
from pyspark.conf import SparkConf
import json


def start_spark(app_name="my_spark_app",master="local[*]",files=['etl_conf.json']):


    flag_repl=not(hasattr(__main__,'__file__'))
    flag_debug='DEBUG' in environ.keys()
        
        
    if not(flag_repl or flag_debug):    
        spark_builder=(SparkSession.builder.appName(app_name))
    else:
        spark_builder=SparkSession.builder.appName(app_name).master(master)
    
    spark_files='.'.join(list(files))
    spark_builder.config('spark.files',spark_files)
    spark_builder.config(conf=SparkConf())    
    spark_sess=spark_builder.getOrCreate()
    
    
    #spark_logger=logger.Log4j(spark_sess)
    spark_files_dir=SparkFiles.getRootDirectory()
    
    config_files=[x for x in listdir(spark_files_dir) if x.endswith('conf.json')]
    
    if config_files:
        path_to_config_file=path.join(spark_files_dir,config_files[0])
        with open(path_to_config_file,'r') as f:
            config_dict=json.load(f)
    else:
        
        config_dict=None
    
    return spark_sess,config_dict
```



ETL Process contains 3 stages: Extract, Transform, Load. In Spark,

- **Extract**: read Parquet format data in local machine
- **Transform**: use sparkSQL to manipulate data
- **Load**: write to csv




```python
from spark import start_spark
from pyspark import SparkConf
from pyspark import SparkFiles
from pyspark.sql import Row


def main():
    spark,conf=start_spark()
    steps_per_floor_=conf['steps_per_floor']
    df=extract(spark)
    df_tf=transform(df,steps_per_floor_,spark)
    load(df_tf)

def extract(spark):
    df=spark.read.parquet('tests/test_data/employees')
    return df

def transform(df,steps_per_floor_,spark):
    df.createOrReplaceTempView("table1")
    df_transformed=spark.sql("select id, concat(first_name,' ' , second_name) as name, floor* %s as steps_to_desk from table1"%steps_per_floor_)
    return df_transformed

def load(df):
    df.coalesce(1).write.csv('loaded_data', mode='overwrite', header=True)

def create_test_data(spark,conf):   
    local_records=[
            Row(id=1, first_name='nancy', second_name="yan", floor=1),
            Row(id=2, first_name='Dan', second_name='Sommerville', floor=1),
            Row(id=3, first_name='Alex', second_name='Ioannides', floor=2),
            Row(id=4, first_name='Ken', second_name='Lai', floor=2),
            Row(id=5, first_name='Stu', second_name='White', floor=3),
            Row(id=6, first_name='Mark', second_name='Sweeting', floor=3),
            Row(id=7, first_name='Phil', second_name='Bird', floor=4),
            Row(id=8, first_name='Kim', second_name='Suter', floor=4)
        ]
    
    df=spark.createDataFrame(local_records)
    df_tf=transform(df,conf['steps_per_floor'],spark)
    df_tf.coalesce(1).write.parquet('tests/test_data/employees_report',mode='overwrite')
```
