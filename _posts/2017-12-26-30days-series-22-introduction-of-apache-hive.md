---
layout:     post
title:      "30天系列 Day 22-Apache Hive 簡介"
subtitle:   "30 Days Series Day 22 - Introduction Of Apache Hive"
date:       2017-12-26 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Apache Hive

Apache Hive 的資料倉儲(Data Warehouse)系統，是一個便於使用SQL語法對巨量資料集進行操作的工具。可以對已經存在的資料定義結構(Schema)後，以該資料結構進行SQL語法的查詢，並且提供command line 工具以及 JDBC driver，讓使用者可以連線到Hive所提供的 service進行資料操作。

Apache Hive 剛開始開發時只是Apache Hadoop的子專案，後來獨立出來就成為了Apache 的top project，其受歡迎的程度可想而知。

Apache Hive可以視為client工具，卻又需要依賴Hadoop運作，具有下列的優點：

* 可以透過SQL來探索資料，做到extract/transform/load (ETL)、報表與資料分析等等，資料倉儲的工作。
* 可加強對於已格式化資料的結構(Schema)。
* 可直接讀取存放在Apache HDFS或是Apache HBase等資料儲存系統的資料。
* 可透過Apache Tez、Apache Spark或是MapReduce來進行查詢。

Hive 的SQL語法符合[標準SQL][sql_standard]，包含最新的SQL-2003與SQL-2011。標準SQL語法會因為Hive版本而有所不同，可以看下列的連結：

* [Apache Hive 2.1][sql_21]
* [Apache Hive 2.3][sql_23]

Hive除了內建的SQL function以外(如 max，avg等等)也可以自定義使用者函式(user defined functions (UDFs))或者user defined aggregates (UDAFs)與user defined table functions (UDTFs)。

假設資料儲存在HDFS，可透過Hive建立meta後以SQL語法進行查詢，其支援的檔案格式可以是CSV/TSV，或者Apache Parquet、Apache ORC等等，甚至可以自行實作支援的資料格式，詳情可以查看[開發者手冊][file_format_develop]。

在接下來的篇章會介紹下列有關Apache Hive的項目：

* Apache Hive 安裝與設定
* Apache Hive SQL基礎教學
* Apache Hive 與 HBase
* Hiveserver2
* Apache Hive On Spark

[sql_standard]: https://cwiki.apache.org/confluence/display/Hive/Apache+Hive+SQL+Conformance
[sql_21]: https://cwiki.apache.org/confluence/display/Hive/Supported+Features%3A+Apache+Hive+2.1
[sql_23]: https://cwiki.apache.org/confluence/display/Hive/Supported+Features%3A+Apache+Hive+2.3
[file_format_develop]: https://cwiki.apache.org/confluence/display/Hive/DeveloperGuide#DeveloperGuide-FileFormats