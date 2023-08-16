---
title: Hadoop MapReduce Streaming Application in Python
date: '2019/06/27 00:00:20'
categories: Big Data
mathjax: true
tags:
  - Hadoop
comments: true
abbrlink: f53c188b
---

>  Study note of [Big Data Essentials: HDFS, MapReduce and Spark RDD](https://www.coursera.org/learn/big-data-essentials)

<!--more-->


# Streaming

In a Hadoop MapReduce application:






1. you have a stream of input key value pairs. 
<img src="./mrs1.png" width="600" />

2. you process this data with a map function, and transform this data to a list of intermediate key value pairs.
  <img src="./mrs2.png" width="600" />
3. This data is aggregated by keys during shuffle and sort phase.
  <img src="./mrs3.png" width="600" />
4. you process data provided in reduce function
  <img src="./mrs4.png" width="600" />

If you want to plug in an external program, then it is natural to communicate via standard input and output channels. You have to *implement your own mappers and reducers* instead of just map and reduce functions.
<img src="./mrs5.png" width="600" />


## What is Hadoop Streaming?
Hadoop streaming is a utility that comes with the Hadoop distribution. The utility allows you to create and run Map/Reduce jobs with any executable or script as the mapper and/or the reducer. For example:
```bash
$HADOOP_HOME/bin/hadoop  jar $HADOOP_HOME/hadoop-streaming.jar \
    -input myInputDirs \
    -output myOutputDir \
    -mapper /bin/cat \
    -reducer /bin/wc
```

## CLI command
### Execute the map phase

1. define the path to the streaming jar
 - Where this jar is located depends on your cluster installation
2. execute yarn application
3. provide a number of arguments
 - mapper: bash command wc-l. 
 - numReducesTasks: For now, you don't want to execute any reducers, 
 - input: specify what HDFS folder or file you are going to process
 - output: specify an HDFS folder for output.


<img src="./s1.png" width="600" />

If you already have this HDFS folder, then you have to remove it beforehand. Otherwise, you wouldn't be able to launch mapreduce jar.

<img src="./s2.png" width="600" />
<img src="./s3.png" width="600" />

**HDFS folder internals after a successful execution**
- only two mappers were executed
- These two files contain information about the processed articles. 
- If you sum them up, then you get 4,100 Wikipedia articles in our assemble.
<img src="./s4.png" width="600" />


### Execute the reduce phase


1. For each input line, sum it up to the variable-->line count
2. provide reducer which aggregates the number of articles from all the mappers-->awk
3. specified exactly one reducer-->guarantee that the only reducer will be working, you have exactly one value in the output.
4. print it out


<img src="./s5.png" width="600" />

Wrap special characters in the Shell map and reduce commands into as Shell script.

<img src="./s6.png" width="600" />

Summary: write and call MapReduce Shell streaming application.
<img src="./s7.png" width="600" />

# Streaming in Python

- If you have more than one file to distribute over the workers, then you can specify them as a comma separated list. 
- 
<img src="./sp1.png" width="500" />

- I have also flipped the order of arguments.The order of some arguments is important. They are the so-called **generic options**.

<img src="./sp2.png" width="600" />

- When released an HDFS folder with output, you see only one file.
 - it is only natural to see an empty output by running this streaming mapReduce job. 
<img src="./sp3.png" width="600" />

 - double check it with an hdfs minus text command.
 <img src="./sp4.png" width="600" />

**mapper.py**
 <img src="./sp5.png" width="600" />

**reducer.py**
 <img src="./sp6.png" width="600" /> 
 <img src="./sp9.png" width="600" />

# WordCount in Python

learn how to define key value pairs for the input and output streams.

## Map

<img src="./wc1.png" width="600" />
1. For each input line, you split it into key and value where the article ID is a key, and the article content is a value. 

2. Then you split the content into words, and finally output intermediate key value pairs.
<img src="./wc2.png" width="600" />

3. validate mapper against a small dataset.
 -  I don't want to execute any reducer. So, I set minus numReduceTasks argument to zero. 
 - In the output folder, you'll see several map output files. According to the random nature, you don't know which of the mappers processed the first split of data. 
<img src="./wc3.png" width="600" />

  - As you can see the first chunk of data was processed by the second mapper. 
  - And there is no article ID in the output.
<img src="./wc4.png" width="600" />

4. If you see one reducer with default implementation which does nothing, then shuffle and sort phase will be executed. And you should see the sorted output.
 - Be cautious to use one reducer with big datasets
 <img src="./wc5.png" width="600" />
 - To get rid of all of the configuration characters. You can use Python regular expression module. I use here Capital W, which helps to ignore all bold characters.
 <img src="./wc6.png" width="600" />
 <img src="./wc7.png" width="600" />

## Reduce
>have data aggregated by key. But how does reading the input streaming data on the reducer side look like.

 <img src="./wc8.png" width="600" />

On reduce phase, you have sorted stream of key value pairs
- stream is sorted by keys-->you can iterate line by line, and keep track of the current keys to aggregate values. 
 <img src="./wc9.png" width="600" />

### Code Snippet
  <img src="./mrs10.png" width="600" />

  If you execute the whole mapper reduce job with one reducer, then you get only one file in the output.
<img src="./mrs11.png" width="600" />

  Take a look at the content of this file, then you see the data sorted by keys, and there is only one value per each key.
<img src="./mrs12.png" width="600" />


**Remove *numReduceTasks* from the argument list**
- my previous job will have an arbitrary number of reducers
- several files in the output HDFS folder. 
- In each file, the data is sorted by keys, but the keys are not globally sorted as I have shuffled between reducers. 

<img src="./mrs13.png" width="600" />

# Distributed Cache

**Distributed Cache** is a facility provided by the Hadoop MapReduce framework. It cache files when needed by the applications. It can cache read only text files, archives, jar files etc. Once we have cached a file for our job, Hadoop will make it available on each datanodes where map/reduce tasks are running.

When you call MapReduce application, NodeManagers provide containers for execution. And there can be several containers on each NodeManager. If you provide files then each of this files will be copied once by each node before any task execution. 
<img src="./dc2.png" width="600" />

So each container can access this data locally via created synlinks. 
- distributed cache file should not be modified by the application while the job being executed.
<img src="./dc3.png" width="600" />

## 3 ways to distribute files
<img src="./dc4.png" width="600" />

- -files
- -archives: provides the ability to better utilize network profile transmission
 - all archives will be un-parked on worker nodes. So, you will be able to work with profiles from mapper or reducer's grid. 
- -libjars: distribute files in JARs(Java Archive). You will not pay attention to this option at all, as this course is about Python development, not Java.

## Archives

1. create two files - The text files will contain female and male names.
2. create a tar archive with the following CLI comment.
<img src="./dv6.png" width="600" />

3. execute the following MapReduce application
<img src="./dc7.png" width="600" />

Python code:

- mapper.py
<img src="./dc8.png" width="600" />

- reducer.py
<img src="./dc10.png" width="600" />

# Environments, Counters

You can get job configuration options through environment variables. When you launch MapReduce application, hadoop framework will assign splits of data to available workers. you can access this data from your scripts.
<img src="./e1.png" width="600" />
<img src="./e2.png" width="600" />
You can also access task id within map, or reduce phase with the following environment variables. 
1. get an absolute task id, and is usually available as a task name on job tracker ui.
2. access the relative order of the task within map or reduced pace. In this example, it would be 1 and 8.
<img src="./e3.png" width="600" />

<img src="./e4.png" width="600" />

In addition to the existing environment variables, you can provide your own:
- Write a generic mapper for word cound problem
 - use regular expression to parse words. 
<img src="./e5.png" width="600" />

 - use -D flag to provide arbitrary environment variables
 - Here, I provided a regular expression to parse words ending with numbers
<img src="./e6.png" width="600" />

## Communication
1. Reading environment variables is a one-way communication between Hadoop MapReduce and scripts.
2. A backward communication channel between framework--provide information about your task progress
<img src="./e7.png" width="600" />

2 types of information:
- status
 - provide an aribtrary message in status for each task execution
 - normally used to inform a user about the processing stage(startup, run, or cleanup)
<img src="./e8.png" width="600" />

- **counters**
 - accumulate statstics of a map and the reduced executions

In word count example, you can easily count the number of words, providing 
1. a counter family name-->group
2. a counter name
3. the value you'd like to add to the counter

<img src="./e9.png" width="600" />
<img src="./e10.png" width="600" />

# Testing

## Unit Testing

Python Testing Tools: Taxonomy, pytest 

### Example of unit testing

<img src="./t1.png" width="600" />

Aim of testing: to validate your scripts in a reproducible environment as close as possible to the production of one.

## Integration Testing
You can validate the whole pipeline with mapper and reducer, or independent mapper or reducer with the hand-crafted input. This type of testing is referred to as an integration testing, because you validate how our mappers and reducers scripts are integrated with Hadoop MapReduce streaming API.

<img src="./t2.png" width="600" />


Drawback: it will be working out of the box if your scripts rely on MapReduce job configuration options

## System Testing
System testing:  execute the whole pipeline end to end

Hadoop MapReduce framework provides an empty config which you can use for HDFS, MapReduce and the Yarn clients.

> see *locate* CLI to *find* a path to an empty config on your Hadoop installation.

<img src="./t3.png" width="600" />

If you provide a Hadoop empty config, then you execute the whole MapReduce application in a **standalone mode**. 
- an HDFS client points out to a local file system
- a node manager is working on the same node
- your streaming scripts will be able to communicate with MapReduce framework via environment variables.
- You will be able to read configuration of variables and validate counters correctness.

<img src="./t4.png" width="600" />

## Acceptance Testing

- Validation of your streaming scripts against a sample dataset to
 - find bugs early without wasting your time and CPU cycles.
- Validation against big datasets and measuring performance or efficiency of your solution
<img src="./t5.png" width="600" />

# Summary
- you know how to use Python unit testing
- you know how to emulate MapReduce locally
with ``` (cat | map | sort | reduce)```
- you know how to run MapReduce
in a standalone mode ( ```hadoop/conf.empty ```)
- you know why you need to execute
MapReduce against sample datasets

