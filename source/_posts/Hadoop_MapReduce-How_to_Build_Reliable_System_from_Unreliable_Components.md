---
title: 'Hadoop MapReduce: How to Build Reliable System from Unreliable Components'
date: '2019–06–27 00:00:20'
categories: Big Data
mathjax: true
tags:
  - Hadoop
comments: true
abbrlink: df5ae10a
---

> Study note of [Big Data Essentials: HDFS, MapReduce and Spark RDD](https://www.coursera.org/learn/big-data-essentials)

<!--more-->

# Unreliable Components

3 different unreliable components in distributed systemsnodes: nodes, links and clock.
Distributed computational systems:
- built from unreliable components
- Cluster nodes can break any time because of power supply, disk damages, overheated CPUs, and so on

3 different unreliable components in distributed systemsnodes: nodes, links and clock.


## Nodes
**3 types of node failures:**

- **Fail-Stop**: if machines get out of service during a computation then you have to have an external impact to bring system back to a working state.
> A system administrator should either 
 - fix the node and reboot the whole system or part of it. 
 Or
 <img src="./week2_0.png" width="550" />
 - retire the broken machine and reconfigure the distributed system

<img src="./week2_1.png" width="550" />


- **Fail-Recovery**: during computations, notes can arbitrarily crash and return back to servers. 
 - doesn't influence correctness and success of computations
 - no external impact necessary to reconfiguring the system at such events.
> if a hard drive was damaged, then a system administrator can physically change the hard drive.After reconnection, this node will be automatically picked up by a distributed system. And it will even be able to participate in current computations.

<img src="./week2_2.png" width="550" />

- **Byzantine**:
A distributed system is robust Byzantine failures if it can correctly work despite some of the nodes behaving out of protocol.
<img src="./week2_3.png" width="550" />

> If you are developing a financial system, then you are likely required to deal with these types of failures to protect your customers and your business.

## Links
**3 types of links:**

- perfect: all the sent messages must be delivered and received without any modification into the same portal.
<img src="./week2_4.png" width="550" />

- fail-loss: some part of the messages can be lost but the probability of message loss does not depend on contents of a message.

> the well-known TCP/IP protocol tries to solve this problem by re-transmitting messages if they were not received.


<img src="./week2_5.png" width="350" />
- byzantine: some messages can be filtered according to some rule, some messages can be modified, and some messages could be created out of nowhere

## Clocks

**clock synchronization problem: **

- clock skew: the time can be different on different machines
- clock drift: there can be a different clock rate
<img src="./week2_6.png" width="300" />

**Logical clocks** help to track happened before events and therefore, order events to build reliable protocols. 

## [A]synchronous systems
Systems can be divided into synchronous and asynchronous.

**synchronous**: 
- every network packet should be delivered within a limited time bound. 
- Clock drift is limited in size
- each CPU instruction is also limited in time.

### Examples of different distributed systems

1. fail-stop, perfect link, and synchronous model

> A parallel computational model and widely adopted by supercomputers where many processors connected by a local high speed computer bus. 

2. fail-recovery, fair-loss link, and asynchronous model (this course focus)

3. byzantine-failure, byzantine link, and asynchronous model

> computational components spread across the globe of unreliable and untrusted network connections. The common representative of this model is grid computing.

# MapReduce


There are two phases during computation, map and reduce:

**map**:apply the same function to each element of your collection.
<img src="./week2_8.png" width="550" />

**reduce**:Reduce operator causes a sequence of elements by applying the following procedure iteratively.
- As soon as you have more than one element in the sequence, then you get the first two and combine them to one element by applying the provided function.
- Reduce function computes the value from left to right.
>Be careful about reducing functions that are not associative.

<img src="./week2_9.png" width="550" />

**MapReduce**:the class of problems that you can solve with arbitrary map and reduce functions is quite big.
<img src="./week2_10.png" width="550" />

# Distributed Shell

- run a distributed **grep** as a MapReduce job.
 <img src="./week2_12.png" width="550" />
 - Map will be equivalent to grep
 - Reduce will be None. 

In MapReduce applications, you don't always need map or reduce function. 

- run a distributed **head** as a MapReduce job.
 <img src="./week2_13.png" width="550" />
You can just retrieve the necessary data with HDFS client. To get these data with MapReduce job, get actual information such as, block index and size in lines on map phase to complete the task correctly --> **head and tweaks**.

- run a distributed **wc** as a MapReduce job.
 <img src="./week2_14.png" width="550" />
 - The output from the map will be a tuple of the size 3: number of lines, words, and bytes.
 - Sum the items by components, so the reduced function will be an extended add operator.


## WordCound Example

*find the most popular words in the Wikipedia with MapReduce*

 1. count how many times each word appear in a data set
> wikipedia.dump | tr ' ' '\n' | sort | uniq -c

Map-->Shuffle&Sort-->Reduce
- Map: the text is split in two words - tr
- Shuffle&Sort: words are distributed to a reduce phase in a way that reduce functions can be executed independently on different machines - sort
- Reduce: uniq

 <img src="./mr2.png" width="600" />
 **external sorting**:If the data is sorted, and can be read as a stream, then uniq-c will be working correctly. To make data sorted, you only need to have enough disk space. The algorithm for this is called, external sorting.

All input and output of map and reduce functions should be a key value pair.

**MapReduce Formal Model**:

map: (key, value) → (key, value)

reduce: (key, value) → (key, value)

```bash
$ cat -n wikipedia.dump | tr ' ' '\n'| sort | uniq -c
```


- read data and get pairs with a line number, and line content.

> cat -n wikipedia.dump: [(line_no, line), …]

- on a map phase,you ignore line numbers and split lines into words. 
> tr ' ' '\n': (-, line) —> [ (word, 1), … ]

 - You can add value one to each output it worked. So, it means that you have seen this word once by reading a line from left to right. 

- a shuffle and sort phase where you spread the words by the hashes.So, you can process them on independent reducers. 
> sort: Shuffle & Sort

- count how many figures of 1 you have for each word, and sum them up to get an answer. 
> uniq -c: (word, [1, …]) —> (word, count)


<img src="./mr4.png" width="600" />

**3 types of key value pairs**:
- Key value pairs for the input data
```bash
cat -n wikipedia.dump: [(line_no, line), …]
read: [(k_in, v_in), …]
```
- key value pairs for the intermediate data
```bash
tr ‘ ‘ ‘\n’: (-, line) —> [ (word, 1), … ]
map: (k_in, v_in) —> [(k_interm, v_interm), …]
```
- key value pairs for the output data
```bash
Shuffle & Sort: sort and group by k_interm
uniq -c: (word, [1, …]) —> (word, count)
reduce: (k_interm, [(v_interm, …)] ) —> [(k_out, v_out), …]
```
<img src="./mr6.png" width="600" />

## Fault Tolerence
In a distributed file system, you store information with duplication in order to overcome node failures. MapReduce framework should also provide robustness against node failures during the job execution.

In a MapReduce job, you will have:
1. Master program: control the execution
<img src="./ft1.png" width="600" />

2. Master program will launch mappers to process input blocks or splits of data. 
<img src="./ft2.png" width="600" />

3. To overcome the issues of correction execution mappers, there is no harm in re-executing mapper against the same data because you expect map function to be deterministic. As soon as you work on top of HDFS, you have a replica of this data on other nodes. So, you could assign another worker to a execute mapper against these data, and application master will do all this magic for you.
<img src="./ft3.png" width="600" />

4. If a worker running reducer dies, you can shuffle and sort data for this particular reducer to another worker. 
<img src="./ft4.png" width="600" />

5.  Shuffled and sorted data are stored on local disks instead of the distributed file system
<img src="./ft5.png" width="600" />

> *You only need to provide deterministic map and reduce function*


## Hadoop MapReduce framework


**Job**
One MapReduce application is a job.

**Task**
a task can be either mapper or reducer.

### First version
<img src="./ft7.png" width="600" />

- JobTracker: one global JobTracker to direct execution of MapReduce jobs.
 - located on one high-cost and high-performance node with HDFS namenode.
 - a single point failure
- TaskTrackers
 - located once per every node where you store data, or where datanode daemon is working
 - spawns workers from mapper or reducer

### YARN
> Yet Another Resource Negotiation

<img src="./ft8.png" width="600" />

- TaskTracker is subtituted by NodeManagers who can provide a layer of CPU and RAM containers.
-  ResourceManager overseas NodeManagers, and client request resources for execution
 - MapReduce applications can work on top of this resource layer.
- There is no concept such as a global JobTracker because application master can start on any node. 
-  All of these enable Hadoop to share resources dynamically between MapReduce and other parallel processing frameworks. 

<img src="./mrf.png" width="600" />

