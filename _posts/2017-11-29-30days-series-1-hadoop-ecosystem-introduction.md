---
layout:     post
title:      "30天系列 Day 1-Hadoop Ecosystem簡介"
subtitle:   "30 Days Series Day 1 - Introduction of Hadoop Ecosystem."
date:       2017-11-29 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Day 1 - Hadoop Ecosystem 簡介

基於 Google 2003 年發表了 MapReduce 與 GFS(Google File System) 的論文，在兩位
Hadoop 創始人 Doug Cutting 與 Mike Cafarella 的努力下，Hadoop 0.1.0 於 2006 年釋
出。    

從 2006 年到現在， Hadoop 依然就像 Doug Cutting 所說的 “ 持續在進步 ”，目前的穩定
版本已經來到了 2.8.2，而 3.0.0 的釋出時間也即將來臨。

在 Big Data 的世界，Hadoop 目前成為不可或缺的主角，而依附在 Hadoop 上所發展出來
的 Project 也非常的多。在接下來的 30 篇文章中，除了 Hadoop 以外還會介紹下列的
Ecosystem Project，內容包含`介紹`、`安裝教學`、`故障排除`與各 `Project 使用情
境`。

* [_Apache Hadoop_][hadoop_official]
  * 除了眾所皆知的 HDFS 外，Yarn 與 MapReduce 都是被廣泛使用的模組。簡單來說
    Hadoop 是一個集`儲存`、`運算`、`資源管理`於一身的分散式 Big Data 處理平臺，
    也因容易擴充 (scale) 與高可用性 (high-availability) 的優點，讓 Hadoop 廣受企
    業用戶的歡迎。
* [_Apache HBase_][hbase_official]
  * 運作在 HDFS 之上的非關連式分散式資料庫 (non-relational, distributed
    database)。想在 Big Data 的世界使用 DataBase，又想擁有 Hadoop 的容錯機制優點
    ，選 HBase 就對了！HBase 具備高吞吐量與低延遲性的特點，非常適合在 Big Data
    進行更快速的讀寫操作。
* [_Apache Hive_][hive_official]
  * 基於 Hadoop 架構的資料倉儲 (data warehouse) 工具，可對儲存於 HDFS 上的結構化
    檔案 ( 如 csv 格式 ) 以 SQL 語法進行查詢與分析。
* [_Apache Spark_][spark_official]
  * 分散式運算的框架，因其使 In Memory 運算的特點，計算效能上比傳統 MapReduce 最
    多快上 100 倍，成為近幾年 Big Data 世界深受歡迎的新寵兒。因原始碼使用
    [_Scala_][scala_official] 撰寫而成，也讓 Scala 使用者在近幾年快速成長。
* [_Apache Solr_][solr_official]
  * 基於[_Apache Lucene_][lucene_official]發展出來的搜尋平台，Apache Lucene 的創
    始者同時也是 Hadoop 之父 Doug Cutting。Apache Solr 提供全文檢索功能，可以在
    數以百萬計的檔案中快速搜尋關鍵字，成為 Hadoop Ecosystem 不可或缺的工具之一。

[hadoop_official]: https://hadoop.apache.org/
[hbase_official]: https://hbase.apache.org/
[hive_official]: https://hive.apache.org/
[spark_official]: https://spark.apache.org/
[scala_official]: https://www.scala-lang.org/
[solr_official]: https://lucene.apache.org/solr/
[lucene_official]: https://lucene.apache.org/
