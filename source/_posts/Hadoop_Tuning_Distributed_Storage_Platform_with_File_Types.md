---
title: 'Hadoop MapReduce: Tuning Distributed Storage Platform with File Types'
categories: Big Data
mathjax: true
tags:
  - Hadoop
comments: true
abbrlink: c490e7f0
---

> Study note of [Big Data Essentials: HDFS, MapReduce and Spark RDD](https://www.coursera.org/learn/big-data-essentials)

<!--more-->

# Data modeling and file formats

There is a mismatch between terms used to define business tasks and terms used to describe what HDFS is. 

Data modeling and data management are concerned with these issues.




<img src="./week_50.png" width="300" />


## Data modeling
- **Data model** – a way you think about your data elements, what they are, what domain they come from, how different elements relate to each other, what they are composed of

 - abstract model
 - explicitly defines the structure of data
 - Makes some things easier to express than others
 - Will use a relational model

- **Relational data model** 

 - a data set is an ordered set called table of tuples called rows. 
 - Where every tuple is composed of simple values such as numbers or strings.
 - A position of a value within a tuple is a column. And column defines value semantics.
<img src="./week_51.png" width="300" />

- **Graph data model**

 - A graph consist of vertices and edges.
 - vertices: represent entities. Movies, actors, directors, titles, and so on.
 - edges: represent relations between entities.

<img src="./week_52.png" width="300" />

- **Unstructured data**

 - Technically, all data is structured at least as a byte sequence
 - Usually, means “not structured enough for a task
 - Denote *complexity of bringing data to useful form for a particular application*.

e.g.
Videos are structured as a sequence of frames, where each frame is an ordered set of pixels, where every pixel is just a triple of RGB color intensities.

However, this structure is useless if you're willing to count people in the video. The hard job is to do the image recognition and bring the appropriate structure to the data so that solving accounting problem would become easier.

## Data Management

> How to store and how to organize your data physically. In Hadoop, this is a matter of file format or storage format

File format:
- Defines (physical) data layout
- Different choices of data layout lead to different tradeoffs in application complexity, and thus, affect performance and correctness.

**Primary function**: 
- **serialization**: a process of converting an object into a sequence of bytes which can be persisted to a disk or database or can be sent through streams. 
- **deserialization**: creating object from sequence of bytes

**Differ in:**
- space efficiency: different formats use different coding schemes which directly affect consumed disk space.
- encoding & decoding speed
- supported data types
- splittable/monolithic structure: this property allows you to extract a subset of data without reading the entire file. We typically expect data to be splittable. This follows from our data model.
- extensibility

# Text Formats

line delimited text files:

- pros: human-readable

- cons: you need to parse it, convert it from the textual form into programmatic data structures.

## CSV
> comma-separated values

Criteria:
- Space efficiency: BAD
- Extensibility: BAD -It is not that easy to remove or reorder fields in these formats and the code is likely to make assumptions about the field indexes.
- Splittable: You should not include any headers in your data as it hinders splittability and mergability of your data.
- Data types: ONLY STRINGS
- Speed: generation and parsing are very efficient


## JSON
>  JavaScript Object Notation: defines a representation for the primitive values and their combination in the form of lists and maps.

Criteria:
- Space efficiency: WORSE THAN CSV -It includes field names in serialized form. As you can see, the strings ticker, date, and others are repeated in every row of the data set.
- Extensibility: You can easily add and remove fields from your data items and JSON will remain valid and parsable.
- Splittable:
 - SPLITTABLE IF 1 DOCUMENT PER LINE
- Data types: JSON allows you to store strings, numbers, Booleans, maps, lists in the native way.
- Speed: GOOD ENOUGH


# Binary formats

- Text formats are designed with human readability in mind
- Binary formats are designed for machines and they trade readability for efficiency and correctness.

## Sequence File

- First binary format implemented in Hadoop
- Stores sequence of key-value pairs, where key and value are of arbitrary type with the user defined serialization and deserialization code
- Java-specific serialization/deserialization
- Primary use case: storing the intermediate data in MapReduce computations

### Data Layout
SequenceFile starts with the header which includes format version, class names for key and value types, flags syndicating compression, metadata, and a sync marker. 

<img src="./week_60.png" width="400" />

Uncompressed case for every record:

- Fixed size header with a record key length and value length, followed by the serialized key and the serialized value. 
- To decode data, you can read the file linearly and use length to read the key and the value.

Block compressed case:

- Key-value pairs are grouped in blocks and every block starts with a number of pairs followed by the key lengths and the compressed keys. Then by value lengths and finally by compressed values. 

Record compressed case:
- Every value is compressed individually while block compressed case, a set of keys or values are compressed together resulting in better compression 

### Critera
- Space efficiency: MODERATE TO GOOD -the on-disk format closely matches the in-memory format to enable fast encoding and decoding. 
- Extensibility: No
- Splittable: splittable via sync markers -Sync markers are unique with the high probability, so you can seek to an arbitrary point in the file and scan for the next occurrence of the sync marker to get the next record.
- Data types: Any type implement in the appropriate interfaces could be used with a format.
- Speed:GOOD 



## AVRO
>  Avro's design goal was to create an efficient and flexible format which could be used with different programming languages.

- Both format & support library
- Stores objects defined by the schema
 - specifies field names, types, aliases
 - defines serialization/deserialization code
 - allows some schema updates
- Interoperability with many languages


**schema**: a description of the fields in data items and their types.

- defines data encoding for every item
-  When storing data, the schema is included in the file thus allowing future readers to decode it correctly
-  If the read schema does not match the data schema, Avro tries to resolve inconsistencies thus enabling smooth schema migrations

### Data Layout

Every Avro file starts with a header followed by a sequence of blocks containing the number of encoded objects, their sizes, and the actual payload. 

**Sync markers** are used to delimit consequent blocks of records. 

What is different in Avro is that the serialization code is defined by the schema and not by the user-provided code. 
<img src="./week_61.png" width="300" />

### Critera
- Space efficiency: MODERATE TO GOOD -The encoding format mostly follows the in-memory format. Space savings could be obtained by using compression.
- Extensibility: field addition, or removal, or renaming, are handled transparently by the framework.
- Splittable: Achieved using the same sync marker technique as in sequence files
- Data types: same types as JSON, plus a few more complex types, like enumerations records.
- Speed: GOOD WITH CODE  GENERATION -Avro can generate serialization and deserialization code from a schema. In this case, its performance closely matches sequence files. 

Avro is a popular format now, holding the balance between efficiency and flexibility.

## RCFile

### Columnar
The **execution time** for analytical applications is mostly I/O bound---you could gain more by optimizing input and output, while optimizing the computation has a diminishing effect on performance.

**How to save input and output operations?**
- not reading the data necessary for the processing
- using superior compression schemes.

RCFile and Parquet: columnar(relational data model) data formats that exploit outlined optimizations.

**Pros of columnar:**
1. Columnar stores are highly efficient in terms of CPU usage.databases were storing data row by row, linearly. They would completely serialize one row before continuing to the other ---> if you need to read the values from just one particular column, you still need to read the whole table.

2. Columnar stores transpose data layout and store all the values column by column, enabling two key optimizations:
 - you can efficiently scan only the necessary data. 
 - you can achieve better compression ratios, because column-wise, data is more regular and more repetitive, and hence, more compressible.

**Cons of columnar:**
1. row assembly -To reconstruct the full row, you need to perform lookups from all the columns, which is likely to cause more input and output operations. However, by accessing the subset of columns, you can reduce the number of input and output operations.




### Data Layout
RC: Record Columnar


- First columnar format* in Hadoop()
- one of the most popular storage formats for data warehouses.
- Horizontal/vertical partitioning to layout data 
 - rows are split into row groups; within each row group, values are encoded column by column.
 - transpose values within a row group
 > The scheme, assuming that the row repeats with a single block managed by a single machine, ensures that the row assembly is a local operation, and hence, does not incur any network accesses.

<img src="./week_70.png" width="300" />

Every row group contains three factions: sync marker, metadata, and column data.

- Metadata: 
 - the number of rows
 - the number of columns
 - the total number of bytes
 - bytes per column
 - bytes per value.

This information is used by a decoder to read the consequent column data.

**Compression:**

- Metadata is compressed with the run-length encoding to save on the repeated integers;

- Column data is compressed with a general-purpose codec such as ZIP.

To produce a block of data, you need to buffer a row group within the main memory and transpose it, and then precompute metadata.

### Critera
- Space efficiency:
 - RCFiles save a lot of space by exploiting the columnar layout
 - data itself is compressed on the block level, increasing space savings. 
- Speed: MODERATE TO GOOD,LESS I/O
 - ZIP is not the fastest codec in the world. 
 - Speed is gained by reducing input and output operations, by not reading columns that are not used in further computation.
- Splittable: SPLITTABLE 
 - sync markers are used to make a splittable format.
- Data types: BYTE STRINGS 
 - RCFiles are untyped. And values are treated as bytes. 
 - The reason for that is because RCFiles are mostly used in conjunction with Hive. And Hive deals with all the serialization and deserialization. So there is no need to offload this functionality to the format.
- Extensibility: NO 
 - Encoded records have a fixed structure. So you need to deal with schema migration by yourself. Once again, this is mostly because Hive rewrites data on schema change.



## Parquet

- The most sophisticated columnar format in Hadoop
- Collaborative effort by Twitter & Cloudera
- Supports nested and repeated data
- Exploits many columnar optimizations (such as predicate pruning,per column codecs)
- Optimizes write path

### Data Layout
<img src="./week_71.png" width="300" />


## Conclusion

- Binary formats are efficient in coding data
 - SequenceFile is a reasonable choice for Java users
 - Avro is a good alternative for many use cases
 - RCFile/ORC/Parquet are best for “wide” tables and analytical workloads

 

# Compression



- Block-level compression
 - used in SequenceFiles, RCFiles, Parquet
 - RCFiles: sync markers and metadata could be used to devise splitting for the dataset without decompressing the entire file.
 - applied within a block of data
 - pros: compression does not hinder the ability to navigate through the file quickly.

- File-level compression
 - a file was compressed before being written.
 - e.g.zip applied to all files in Unix systems.
 - applied to the file as a whole
 - hinders an ability to navigate through file
 - pros: achieves better compression ratios
 - cons: breaks the file structure and makes the files lesss splitable. 

## Codecs

In Hadoop stack, there are few popular codecs that you can use with your data: Gzip, Bzip2, LZO, Snappy. 

<img src="./week_80.png" width="400" />

- Bzip2 is the slowest yet the most efficient
- Snappy is the fastest, yet not so efficient

## When to use compression?

1. **CPU-bound**: Assume your program can process data at a rate 10 MB per second.


 - Providing data at higher rates, has no effect on completion time because there is no capacity to perform more work in a unit of time. 
(Your program spends more time computing rather than doing I/O operations.)
 - CPU is your bottleneck
 - Results: Adding compression would put more pressure on CPU and increase the completion time.

<img src="./week_81.png" width="400" />


2. I/O bound: your program can process data at a rate 1000 MB per second, while HDFS can stream data only at rate 100 MB per second

 - your program spends more time waiting for input and output, rather than doing actual computation.
<img src="./week_82.png" width="400" />

 - Adding compression here would allow HDFS to stream the compressed data at rate 100 MB per second, which transforms to 500 MB per second of uncompressed data, assuming the compression ratio of five
<img src="./week_83.png" width="400" />

## Summary

1. Raise awareness about application bottlenecks

 - CPU-bound : cannot benefit from the compression
 - I/O-bound : can benefit from the compression
  >trade spare CPU time that was wasted in I/O wait for an extra work for compression which resulted in better utilization of both CPU and I/O. 

2. Codec performance vary depending on data, many options available

# Conclusion
- Many applications assume relational data model
 - File format defines encoding of your data
 - text formats are readable, allow quick prototyping, but inefficient
 - binary formats are efficient, but more complicated to use
- File formats vary in terms of space efficiency, encoding & decoding speed, support for data types, extensibility
 - When I/O bound, can benefit from compression
 - When CPU bound, compression may increase completion time

