---
layout:     post
title:      "30天系列 Day 16-Apache Spark RDD 簡介"
subtitle:   "30 Days Series Day 16 - Introduction Of Apache Spark RDD"
date:       2017-12-120 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Spark RDD 簡介

Spark 的核心是 RDD，Resilient Distributed DataSet的縮寫，是一種具有容錯(tolerant)與    
高效能(efficient)的抽象資料結構。RDD 由一到數個的 partition組成， Spark程式進行運算時，    
partition會分散在各個節點進行運算，預設會被存放在記憶體內，所以可以快速分享各個partition的運算結果，    
但若記憶體不足會出現`OOM Exception`錯誤訊息，可透過參數設定存放在硬碟避免發生該錯誤。    

RDD支援下列語言撰寫而成的object：

* Scala
* Java
* Python
* R

Spark 是由 Scala 撰寫而成，嚴格遵守Functional Program的概念，所以RDD只能讀取無法寫入。    
Spark 支援讀取 HDFS 等分散式儲存裝置的檔案，故可以使用HDFS的特性便於進行分散式的運算。

綜合以上可歸納出RDD具有幾個特性：

* Immutable
* Distributed
* Parallelizing

每個RDD會紀錄五件事情，分成兩種類別：
* i) Lineage
  * 1.Set of partitions("splits" in Hadoop).
  * 2.List of dependencies on parent RDDs.
  * 3.Function to compute a partition(as an Iterator) given its parent(s).
* ii) Optimized Execution
  * 4.(Optional) partitioner (hash, range).
  * 5.(Optional) preferred location(s) for each partition.

Lineage 為 RDD的血統關係，主要用來作為容錯處理，先來看一段程式碼：

```scala
//RDD Transformations
words = sc.textFile("hdfs://large/file")
          .map(_.toLowerCase)
          .flatMap(_.split(" "))
alpha = words.filter(_.matches("[a-z]+"))
nums  = words.filter(_.matches("[0-9]+"))

//RDD Action
alpha.count()
```