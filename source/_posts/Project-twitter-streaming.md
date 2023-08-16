---
title: Realtime Twitter Data Analysis using Spark Streaming
categories: Project
mathjax: true
comments: true
tags: Spark
abbrlink: 9fb5a802
date: 2019/10/19
---



In this project, I built an application that extract streaming tweets from Twitter, transform the data, and visualize using Apache Sparking Streaming to gain the trending hashtags of a specific topic. In particular, I used a window size of 5 minutes to always get the latest 5 minutes result.

<!--more-->

# Apache Spark Streaming

Spark Streaming is an extension of the core Spark API that enables scalable, high-throughput, fault-tolerant stream processing of live data streams. Data can be ingested from many sources like Kafka, Flume, Kinesis, or TCP sockets, and can be processed using complex algorithms expressed with high-level functions like `map`, `reduce`, `join` and `window`. 



I will skip the explaination of how to set up spark in local machine, and the details of Streaming API. Please see the [document](https://spark.apache.org/docs/latest/api/python/pyspark.streaming.html) of Spark.



# Part I. Create Twitter Streaming



## Register Twitter App

 Before using Twitter's API, I registered an app [here](https://developer.twitter.com/en/apps), and got the *Consumer API keys*, *Access token & access token secret*.

<img src="./2.jpg" width="500" />

These information should be saved, as Twitter needs them to authenticate a user.



## Extract Tweets from Twitter Streaming

I used Twitter API Python wrapper [python-twitter](https://github.com/bear/python-twitter) to get Tweets stream. Here's a snippet of the code:

```python
def twt_app(TCP_IP,TCP_PORT,keyword=KEY_WORD):
    consumer_key=''
    consumer_secret=''
    access_token=''
    access_token_secret=''
    
    api = twitter.Api(consumer_key=consumer_key,
                      consumer_secret=consumer_secret,
                      access_token_key=access_token,
                      access_token_secret=access_token_secret,
                      sleep_on_rate_limit=True)

    LANGUAGES = ['en']
    
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.bind((TCP_IP, TCP_PORT))
    s.listen(10)    
    conn, addr = s.accept()
    
    for line in api.GetStreamFilter(track=[keyword],languages=LANGUAGES):
        conn.send( line['text'].encode('utf-8') )
        print(line['text'])
        print()
```

Basically, I get the streaming of tweets from Twitter API, extract each tweet's text content, and send them to Spark Streaming instance via TCP connection.



Let's try a topic as **"Trump"**! Here is the result recorded in console:

<img src="./3.gif" width="750" />





# Part II. Set Up Streaming Application



Then I set up Spark Streaming App to process tweets text, gain hashtags in every tweet mentioned **"Trump"**, and barplot the top 20 hashtags on the times they appeared in most recent 5 minutes. Here's a snippet of the code:



```python
def spark(TCP_IP,TCP_PORT,KEY_WORD):
    sc=SparkContext(appName="TwitterStreamming")
    sc.setLogLevel("ERROR")
    ssc=StreamingContext(sc,5)
    
    socket_stream = ssc.socketTextStream(TCP_IP,TCP_PORT)
    
    lines=socket_stream.window(300)
    df=lines.flatMap(lambda x:x.split(" "))  \
            .filter(lambda x:x.startswith("#"))  \
            .filter(lambda x:x!='#%s'%KEY_WORD)  
    
    def process(rdd):
        spark=SparkSession \
                .builder \
                .config(conf=rdd.context.getConf()) \
                .getOrCreate()
    
        rowRdd = rdd.map(lambda x: Row(word=x))
        wordsDataFrame = spark.createDataFrame(rowRdd)
    
        wordsDataFrame.createOrReplaceTempView("words")
        wordCountsDataFrame = spark.sql("select word, count(*) as total from words group by word order by 2 desc")       
        pd_df=wordCountsDataFrame.toPandas()
        
        plt.figure( figsize = ( 10, 8 ) )
        sns.barplot( x="total", y="word", data=pd_df.head(20))
        plt.show()
        
    df.foreachRDD(process)
    
    ssc.start()
    time.sleep(600)
    ssc.stop(stopSparkContext=True)
```



Let's see what I got!

<img src="./4.gif" width="750" />





# Summary

Thanks to Twitter API and its python Wrapper, I was able to easily get tweets streaming filtered on specified topic. Real-time ETL process could be used to provide instantaneous recommendation, anomaly detection, and etc. There are various projects around Twitter Streaming to be explored. I, in this post, tried a very simple application to find the real-time hashtags trending around a topic.

As I filtered tweets on topic **"Trump"** , I got *#WGDP*, *#USWNT* to be two of the most trending hashtags during the time I ran the application. This application could catch big hot news, and it also serves as a great way to know about what people are talking about in a smaller topic.



Please check the full code on [GitHub](https://github.com/nancyyanyu/mini_projects/tree/master/twitter_project).