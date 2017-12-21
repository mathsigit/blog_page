---
layout:     post
title:      "30天系列 Day 17-Apache Spark Shell 簡介"
subtitle:   "30 Days Series Day 17 - Introduction Of Apache Spark Shell"
date:       2017-12-21 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Spark Shell

Spark Shell是一個互動介面，提供使用者一個簡單的方式學習Spark API，可以使用Scala或是Python。
要如何運作呢？

首先到官方網站下載Spark，作者這裡使用的Spark 2.1.2 Pre-build for Hadoop 2.7，表示支援Hadoop 2.7或以上到版本。

接著執行下列的語法：

```bash
#若環境變數沒有設定SPARK_HOME，請自行移動至Spark 目錄下。
cd $SPARK_HOME
./bin/spark-shell
```

可以看到下面的歡迎畫面：

```bash
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
Setting default log level to "WARN".
To adjust logging level use sc.setLogLevel(newLevel). For SparkR, use setLogLevel(newLevel).
17/12/21 14:58:18 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
Spark context Web UI available at http://10.1.1.123:4040
Spark context available as 'sc' (master = local[*], app id = local-1513839499523).
Spark session available as 'spark'.
Welcome to
      ____              __
     / __/__  ___ _____/ /__
    _\ \/ _ \/ _ `/ __/  '_/
   /___/ .__/\_,_/_/ /_/\_\   version 2.1.2
      /_/

Using Scala version 2.11.8 (Java HotSpot(TM) 64-Bit Server VM, Java 1.8.0_112)
Type in expressions to have them evaluated.
Type :help for more information.

scala>
```

注意一下啟動時的資訊:

```bash
Spark context Web UI available at http://10.1.1.123:4040
Spark context available as 'sc' (master = local[*], app id = local-1513839499523).
Spark session available as 'spark'.
```

表示已經幫使用者啟動一個 local 的 Spark Web UI，也初始化了Spark context，表示隨時都可以寫一段程式讓 Spark 在 local 進行運作。接著讓我們來寫一段簡單的word count程式碼吧！

## 基礎用法

```scala
#讀取 $SPARK_HOME 根目錄下的 README.md 檔案。
scala> val textFile = sc.textFile("README.md")
textFile: org.apache.spark.rdd.RDD[String] = README.md MapPartitionsRDD[1] at textFile at <console>:24

#計算有多少文字
scala> textFile.count()
res0: Long = 104
```

Spark Application 的運作過程可以在Spark context Web UI可以看到剛剛wordcount運作的資訊。

![spark_shell_web_ui.png](https://raw.githubusercontent.com/mathsigit/blog_page/gh-pages/img/30_days/spark_shell_web_ui.png)

## 進階使用

也可以在 Spark-Shell 內 import 某些 Scala 或 Java 的 Library。

```scala
scala> import java.lang.Math
import java.lang.Math
```

接著來計算README.md內以行(row)為比較單位，每行最多有幾個字：

```scala
scala> val maptextFileRDD = textFile.map(line => line.split(" ").size)
maptextFileRDD: org.apache.spark.rdd.RDD[Int] = MapPartitionsRDD[3] at map at <console>:27

scala> val reduceMaptextFileRDD= maptextFileRDD.reduce((a, b) => Math.max(a, b))
reduceMaptextFileRDD: Int = 22
```

由此可了解單行最多的字有22個。

如果要列印出RDD內容，可以使用下列語法：

```scala
scala> maptextFileRDD.collect().foreach(println)
```

## 實作Hadoop MapReduce

Big Data 的運算框架 MapReduce 一樣可以使用Spark來實作：

```scala
scala> val testFlatmap = textFile.flatMap(line => line.split(" ")).map(word => (word, 1))
testFlatmap: org.apache.spark.rdd.RDD[(String, Int)] = MapPartitionsRDD[8] at map at <console>:27

scala> val reduceByKeyFlatmap = testFlatmap.reduceByKey((a, b) => a + b)
reduceByKeyFlatmap: org.apache.spark.rdd.RDD[(String, Int)] = ShuffledRDD[11] at reduceByKey at <console>:29

//查看結果
scala> reduceByKeyFlatmap.collect()
res5: Array[(String, Int)] = Array((package,1), (this,1), (Version"](http://spark.apache.org/docs/latest/building-spark.html#specifying-the-hadoop-version),1), (Because,1), (Python,2), (page](http://spark.apache.org/documentation.html).,1), (cluster.,1), (its,1), ([run,1), (general,3), (have,1), (pre-built,1), (YARN,,1), ([http://spark.apache.org/developer-tools.html](the,1), (changed,1), (locally,2), (sc.parallelize(1,1), (only,1), (locally.,1), (several,1), (This,2), (basic,1), (Configuration,1), (learning,,1), (documentation,3), (first,1), (graph,1), (Hive,2), (info,1), (["Specifying,1), ("yarn",1), ([params]`.,1), ([project,1), (prefer,1), (SparkPi,2), (<http://spark.apache.org/>,1), (engine,1), (version,1), (file,1), (documentation,,1), (MASTER,1), (example,3), (["Parallel,1), (are...
```

## 離開Spark Shell

使用下面指令就可以離開Spark Shell的介面：

```bash
scala> :q
```

# 最後

Spark Shell 可以讓使用者快速了解如何撰寫RDD並得到結果，不適用於大量運算。如果需要計算大量資料，建議撰寫 Scala 或是 Java 程式，編譯後打包並使用spark-submit 指令送至資源管理系統進行分散式計算，效能會比較好。

簡單介紹完Spark Shell後，接下來要來介紹如何使用Spark api撰寫第一個Hello World application。