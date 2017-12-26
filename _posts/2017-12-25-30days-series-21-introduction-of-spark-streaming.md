---
layout:     post
title:      "30天系列 Day 21-Apache Spark Streaming 簡介"
subtitle:   "30 Days Series Day 21 - Introduction Of Apache Spark Streaming"
date:       2017-12-25 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Spark Streaming

Spark streaming是以Spark核心API擴充出來的一個模組，他在處理資料串流(streaming)上具有可擴充性、高吞吐量、高容錯性特點。可以從Kafka，Flume，Kinesis或TCP等許多來源介接資料，也可以透過Spark API的map，reduce，join和window等函數進行複雜的運算來處理資料。最後再將運算結果送到檔案系統(如HDFS)、資料庫或是即時的監控系統，也可以將資料餵給機器學系的系統，進行即時的運算。

Spark streaming提供了一個高層級的抽象層，稱之為discretized stream(DStream)，意指連續的資料串流。DStream可以通過Kafka，Flume和Kinesis等資料來源來建立，也可以通過在其他DStream的API來新增。在Spark streaming中，一個DStream即為一種有順序的RDD。

目前Spark streaming可以使用Scala，Java或Python（Spark 1.2開始支援）撰寫應用程式。

現在就來看個範例程式吧！下列的程式碼是在Spark 2.1.2執行。

```java
import org.apache.spark._
import org.apache.spark.streaming._

val conf = new SparkConf().setMaster("local[2]").setAppName("NetworkWordCount")
val ssc = new StreamingContext(conf, Seconds(1))

//建立一個DStream來表示來自TCP的data source，指定為主機名（例如localhost）和port（例如9999）。
val lines = ssc.socketTextStream("localhost", 9999)

// 以空白切開每行的字串
val words = lines.flatMap(_.split(" "))

// 計算每批資料的文字數量
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)

// 印出DStreamPrint到console視窗
wordCounts.print()

ssc.start()             // 開始計算
ssc.awaitTermination()  // 等待資料傳送到command視窗
```

如果已經從官方網站下載Spark，上列的程式碼已經被編譯並放入裡面，可以透過下面的指令以local模式來啟動這個Saprk streaming的程式：

```bash
./bin/run-example streaming.NetworkWordCount localhost 9999
```

啟動之後我們需要一個tcp的server來傳入資料，可以使用`nc`這個工具來偽裝一個tcp伺服器輸入資料，開啟另一個command視窗來使用下來指令：

```bash
nc -lk 9999
```

啟動後，可以在裡面輸入資料，這樣另一個Spark streaming的視窗就會顯示由tpc server傳入的文字。

# 最後

看完的入門的Spark streaming，Spark的簡介也告一段落了，如果想看更進階的使用方法，可以到[Apache Spark官方網站](https://spark.apache.org/streaming/)查看文件，官方文件蠻齊全的，如有問題也可以到[Spark 社群](https://spark.apache.org/community.html)討論，或者在者邊留言。

接下來，我們要邁入最後的篇章Apache Hive。