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

```bash
#讀取 $SPARK_HOME 根目錄下的 README.md 檔案。
scala> val textFile = sc.textFile("README.md")
textFile: org.apache.spark.rdd.RDD[String] = README.md MapPartitionsRDD[1] at textFile at <console>:24

#計算有多少文字
scala> textFile.count()
res0: Long = 104
```

Spark Application 的運作過程可以在Spark context Web UI可以看到剛剛wordcount運作的資訊。
