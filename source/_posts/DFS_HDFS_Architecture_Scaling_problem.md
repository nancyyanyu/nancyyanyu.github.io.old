---
title: 'DFS, HDFS, Architecture, Scaling problem'
date: '2019/06/27 00:00:16'
categories: Big Data
mathjax: true
tags:
  - HDFS
  - Hadoop
comments: true
abbrlink: 49a14c15
---

> Study note of [Big Data Essentials: HDFS, MapReduce and Spark RDD](https://www.coursera.org/learn/big-data-essentials)

<!--more-->


# Scaling DFS

## Big data storage: 
- Scale up (vertical scaling): get yourself a bigger hard drive
 - Lower latency
- Scale out (horizontal scaling)
 - Higher latency
 - Problem: one node get out of service 3 years averagely

-> Distributed file system



<img src="./week_1.png" width="600" />



## Google File System:

**Keys:**
- components failures are a norm (→ replication)
- even space utilisation: all files splited into blocks of fixed size, about 100mb
- write-once-read-many: it's not allowed to modify in the middle, as it drastically simplifies API and internal implementation of a distributed file system.

**Replication:**
<img src="./week_2.png" width="600" />
> S.txt and B.txt. They are both split into equal sized blocks, and then distributed over a different machine, with replications. Storage machines are called *channel servers*, or *data nodes*.


**Metadata:** include administrative information about creation time, access properties...

**Master node:** stores all metadata in memory; enable to request metadata with minimal latency


## Hadoop Distributed File System
> an open source implementation of Google File System

**Server roles**:
1. Namenode: master node
2. Datanode

<img src="./week_3.png" width="500" />

HDFS client provides command line interface to communicate with that distributed file system ->no need to write any code to access data.

### How to read files from HDFS?

<img src="./week_4.png" width="600" />

1. Request name node to get information about file blocks' locations. 
>These blocks are distributed over different machines, but all of this complexity is hidden behind HDFS API.


2. User only sees a continuous stream of data. 
>If at some point a datanode you retrieve data from died you get this data from another replica without bothering users about it. 

3. You will get data from the closest machine.

#### Closeness
> data center topology; it depends on the physical distance and unpredictable system load such as metric overutilization

<img src="./week_5.png" width="600" />

- d=0: request data from HDFS, and this data is available on the same machine, then you can use data locality to read data directly from hard drive without any extra RPC codes.
- d=2: a datanode is located in the same rack
- d=4: read data from another rack
- d=6: the data is allocated in another data center

### How to write files into HDFS?

#### Redundancy model:
>When you write a block of data into HDFS, Hadoop distributes replicas over the storage. 

**first replica**: located on the same node if write data from a DataNode machin; otw, the first DataNode to put replica is chosen by random.

**second replica**: placed in a different rack. If this racks goes down (power supply problems), you will access data from another rack.

**third replica**: located on a different machine in the same rack as the second replica. You don't pay for extra between rack network utilization as the third replica is copied from the second data node.

**further replicas**: applies on the random nodes in the cluster

<img src="./week_6.png" width="400" />

#### Data flow of writing data

<img src="./week_7.png" width="500" />

1. HDFS client request and name node via RPC protocol.
> The name node validates if you have rights to create a file and there are no naming conflicts. 

2. HDFS client requests a list of datanodes to put a fraction of blocks of the file. 
> These datanodes form a pipeline as your first client sends packets of data to the closest datanode. The later one transfers copies of packets through a datanode pipeline. As soon as packet is on all of the datanodes, datanodes send acknowledgment packets back.

**If something goes wrong** - then if the client closes the datanode pipeline, marks the misbehaving datanode bad and requests a replacement for the bad datanodes from a name node. So a new data node pipeline will be organized, and the process of writing the file to HDFS will continue.

#### What happens with failure blocks?

<img src="./week_8.png" width="600" />
Datanode serves a state machine for each block. Whenever a datanode recovers from its own failure, or failures of other datanodes in a pipeline, you can be sure that all the necessary replicas will be recovered. And unnecessary ones will be removed.


# Block and Replica States

**Replica** : a physical data storage on a data node.There are usually several replicas with the same content on different data nodes.

**Block**: a meta-information storage on a name node and provides information about replica's locations and their states.

Both replica and block have their own states. 
<img src="./week_9.png" width="400" />

**Data node replica's states**: Finalized, Replica Being Written to, Replica Under Recovery, Replica Waiting to be Recovered, Temporary

**Name node replica's states**: 

**Difference of Datanode & Namenode**: a block state is stored in memory, it doesn't persist on any disk.

## Datanode State: Finalized
<img src="./week_11.png" width="450" />

**Finalized state**: the content of this replica is frozen
- Meaning: meta-information for this block on name node is aligned with all the corresponding replica's states and data. 

- **Read consistency**: you can safely read data from any data node and you will get exactly the same content. 

- **Generation Stamp(GS)**: Each block of data has a version number called Generation Stamp. All of finalized replicas have the same GS number which can only increase over time.
<img src="./week_10.png" width="600" />
-  It happens during error *recovery process* or during *data appending to a block*.

## Datanode State: Replica Being Written
<img src="./week_12.png" width="450" />

**RBW**:the state of the last block of an open file or a file which was reopened for appending.

- Different data nodes can return to use a different set of bytes. In short, bytes that are acknowledged by the downstream data nodes in a pipeline are visible for a reader of this replica. 
- Data node on disk data and name node meta-information may not match during this state.
- **Data Durability**:  In case of any failure data node will try to preserve as many bytes as possible. 


## Datanode State: Replica Waiting to be Recovered

<img src="./week_13.png" width="450" />

**RWR**: a state of all Being Written replicas after data node failure and recovery after a system reboot or after Pacer.sys or BSOD,

- RWR replicas will not be in any data node pipeline and therefore will not receive any new data packets. 
- RWR either become ***outdated and should be discarded***, or they will participate in a special recovery process called a ***lease recovery*** if the client also dies. 


HDFS client requests a _lease_ from a name node to have an exclusive access to write or append data to a file. In case of HDFS client lease expiration, replica transition to a RUR state.


## Datanode State: Replica Under Recovery
<img src="./week_14.png" width="450" />

**RUR** (Replica Under Recovery): HDFS client requests a ***lease*** from a name node to have an exclusive access to write or append data to a file. In case of HDFS client ***lease expiration***(usually happens during the client's site failure), replica transition to a RUR state.

## Datanode State: Temporary
<img src="./week_15.png" width="450" />

**Temporary**: As data grows and different nodes are added or removed from a cluster, data can become unevenly distributed over the cluster nodes. A Hadoop administrator can spawn a process of data re-balancing or a data engineer can request increasing of the replication factor of data for the sake of durability. In these cases new generated replicas will be in a state called temporary. 


- Similar to RBW except the fact that this data is not visible to user unless finalized. 
- In case of failure, the whole chunk of data is removed without any intermediate recovery state.

## Namenode State: Under Construction

<img src="./week_16.png" width="450" />

**Under Construction**: opens a file for writing, name node creates the corresponding block with the *under_construction* state; opens a file for append name node also transition this block to the state *under_construction*. 

- always the last block of a file
- it's length and generation stamp are mutable



## Namenode State: Under Recovery
<img src="./week_17.png" width="450" />

Name node block keeps track of write pipeline. It means that it contains information about all RBW and RWR replicas. Replicas transitions from RWR to recovery RUR state when the client dies. Even more generally it happens when a client's lease expires.
Consequently, the corresponding block transitions from under_construction to under_recovery state. 
<img src="./week_18.png" width="450" />

## Namenode State: Committed
<img src="./week_19.png" width="450" />
The under_construction block transitions to a committed state when a client successfully requests name node to close a file or to create a new consecutive block of data.

The committed state means that there are already some finalized replicas but not all of them. For this reason in order to serve a read request, the committed block needs to keep track of RBW replicas, until all the replicas are transitioned to the finalized state and HDFS client will be able to close the file.

## Namenode State: Final Complete

<img src="./week_20.png" width="500" />

**Final complete state** of a block: a state where all the replicas are in the finalized state and therefore they have identical visible length and generation stamps. 

- Only when all the blocks of a file are complete the file can be closed. 


## Namenode State: Open File

**Open File state**: In case of name node restart, it has to restore the open file state. All the blocks of the un-closed file are loaded as complete except the last block which is loaded as under_construction. 


Then recovery procedures will start to work. 

**Recovery**: replica recovery, block recovery, lease recovery, and pipeline recovery.

# Recovery Process

## Block Recovery

**Goal**: NameNode has to ensure that all of the corresponding replicas of a block will transition to a common state logically and physically. 

**physically**: all the correspondent replicas should have the same on disk content. 

To accomplish it,

1. **primary datanode(PD)**: NameNode chooses a primary datanode called PD in a design document. PD should contain a replica for the target block. 
<img src="./week_21.png" width="600" />


2. PD request from a NameNode, a new generation stamp, information and location of other replicas for recovery process.

<img src="./week_22.png" width="600" />

3. PD connects each relevant DataNodes to participate in the **replica recovery process**.During this phase, all the necessary information or data is propagated through the pipeline. 

**Replica recover process** includes:
- Aborting active clients right into a replica. 
- Aborting the previous replica of block recovery process, and participating in final replica size agreement process. 
<img src="./week_23.png" width="600" />

4. As the last step, PD notifies NameNode about the result, success or failure. In case of failure, NameNode could retry block recovery process.
<img src="./week_24.png" width="600" />


## Lease Recovery

> Block recovery process could happen only as a part of the lease recovery process.

Lease manager manages all the leases at the NameNode. HDFS clients request at least every time they would like to write, or append to a file.
<img src="./week_25.png" width="600" />

### Conditions of starting lease recovery process

Lease manager maintains a soft and a hard limit. If a current lease holder doesn't renew his lease during the soft limit timeout, then another client will be able to take over this lease. In this case and in the case of reaching a hard limit, the process of lease recovery will begin.
<img src="./week_26.png" width="600" />
<img src="./week_28.png" width="600" />

**Necessity:** to close open files for the sake of the client.

**Gurantees to be reached**:

- **concurrency control**: Even if a client is still alive, it won't be able to write data to a file

- **consistency guarantee**: All replicas should draw back to a consistence state to have the same on-disk data and generation stamp.

<img src="./week_29.png" width="600" />

### Lease recovery process
<img src="./week_30.png" width="600" />

1. Lease recovery starts with a **lease renew**. 

New files lease holder should have the ability to take ownership of any other user's lease.
The name of the super user is DFS. Therefore, all the other client request such as get new generation stamp, get new block, close file from other clients to this pass will be rejected.

2. NameNode gets the lease of DataNodes which contains the last block of a file, and sends a primary DataNode and starts a block recovery process. 
<img src="./week_30.png" width="600" />

3. As soon as block recovery process finishes, the NameNode is notified by PD about the outcome. Updates blocking for, and removes the lease for a file.

<img src="./week_31.png" width="600" />

## Pipeline Recovery

### Pipeline

When you write to an HDFS file, HDFS client writes data block by block. Each block is constructed through a **write pipeline**, as the first client breaks down block into pieces called **packets**. These packets are propagated to the DataNodes through the pipeline.

> Three stages: Pipeline setup, data streaming, and close

- bold lines: data packets
- doted lines: acknowledge messages
- regular lines: control messages.

<img src="./week_32.png" width="600" />

#### Setup

a clients sends a setup message down to the pipeline. Each DataNode opens a replica for writing and sends ack message back upstream with the pipeline.
<img src="./week_33.png" width="600" />

#### Data streaming


Data streaming stage is defined by time range from t1 to t2, where t1 is the time when a client to receives the acknowledgement message for the top stage. And t2 is the time when the client receives the acknowledgement message for all the block packets.
<img src="./week_34.png" width="600" />

- data is buffered on the client site to form a packet, then propagated through the DataNode pipeline.

- Next packet can be sent even before the acknowledgment of the previous packet is received.

**flush**: synchronous packets and used as synchronization points for the DataNode right. 

<img src="./week_35.png" width="600" />

#### Close

Finalize replicas and shut down the pipeline.

<img src="./week_36.png" width="600" />

- All of the DataNodes in the pipeline change the replica state to the finalized. 
- Report the state to a NameNode and send the acknowledgement message upstream. 


### Pipeline recovery process

Pipeline recovery can be initiated during each of these stages.


#### Setup failure
A failure happens during writing to a new file, abandon DataNode pipeline and request a new one from scratch
<img src="./week_37.png" width="600" />

In this case, some packets can be resent but they will not be extra disk IO overhead for DataNodes that already saved this packet on disk. Once the client detects a failure during close stage, it rebuilds a pipeline with good DataNodes. Bumps generation stamp and requests to finalize replicas.

#### Data streaming failure
If DataNode is not able to continue process packets appropriately,then it allots the DataNode pipeline about it, by closing all the connections.

When HDFS client detects a fire, it stops sending new packets to the existing pipeline, request a new generation stamp from a NameNode, and rebuilds a pipeline from good DataNodes.
> In this case, some packets can be resent but there will not be extra disk IO overhead for DataNodes that already saved this packet on disk. 

 All DataNodes keep track of bytes received, bytes written to a disk and bytes acknowledged.
<img src="./week_38.png" width="600" />

#### Close failure

Once the client detects a failure during close stage, it rebuilds a pipeline with good DataNodes. Bumps generation stamp and requests to finalize replicas.
<img src="./week_39.png" width="600" />

# HDFS Client


# Namenode Architecture

**NameNode**: a service responsible for keeping hierarchy of folders and files.

- NameNode stores all of this data in memory.

<img src="./week40.png" width="600" />

## Capacity
For example: 10 petabytes of data.

**Capacity for data nodes:**
- In HDFS, all data is usually stored with replication factor three.
> So, you can request to buy approximately 15,000 of two terabyte hard drives.

- On average, 15 hard drives will die every day. 
>So, you should request at least 1000 extra hard drives for several months of research. 

**Capacity for meta information in memory:**

- **Small files problem:** Storing lot of small files which are extremely smaller than the block size cannot be efficiently handled by HDFS. Reading through small files involve lots of seeks and lots of hopping between data node to data node, which is inturn inefficient data processing.

- **128 megabyte** of data was once chosen as a default block size.

>WHY? It is one choice to have less than one percent overhead for reading the random block of data from a hard drive, and keeping block size small at the same time.

<img src="./week_41.png" width="600" />

10 petabytes data will consume at least 35 gigabyte of RAM on the NameNode.

## Failure

**NameNode server is a single point of failure.**
> In case this service goes down, the whole HDFS storage becomes unavailable, even for read-only operations.

Technical tricks to make NameNode decisions durable and to speed up NameNode recovery process:
-  write-ahead log (WAL): strategy to persist matter information modifications. This log is called the edit log. It can be replicated to different hard drives. It is also usually replicated to an NFS storage. 

- NFS storage:you will be able to tolerate full NameNode crash

- **fsimage**: have a snapshot of memory at some point in time from which you can replay transaction stored in the edit log. 

<img src="./week41.png" width="600" />

**secondary NameNode**

> tackle the problem that edit log grows fast, replay of one week of transactions from edit log will take several hours to boot a NameNode

Secondary NameNode, or better to say, checkpoint NameNodes, compacts the edit log by creating a new fsimage.

New fsimage is made of all the fsimage by applying all stored transactions in edit log.

- secondary NameNode consumes the same amount of RAM to build a new fsimage.
- secondary NameNode was considered a badly named service.
- secondary NameNode $\neq$ backup NameNode

<img src="./week42.png" width="600" />


## Evaluate
Evaluate how long it takes to read 10 petabytes of data from a hard drive with similar reading speed:

<img src="./week43.png" width="600" />

The amount of drives in your cluster has a linear relation to the speed of data processing


## Summary
1. explain and reason about HDFS Namenode architecture: (RAM; fsimage + edit log; block size)
2. estimate required resources for a Hadoop cluster
3. explain what small files problem is and where a bottleneck is
4. list differences between different types of Namenodes (Secondary / Checkpoint / Backup

