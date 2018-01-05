---
layout:     post
title:      "30天系列 Day 28-Use Case Of Hadoop Ecosystem"
subtitle:   "30 Days Series Day 28 - Use case Of Hadoop Ecosystem"
date:       2018-01-01 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

介紹完Hadoop Ecosystem的Hadoop、HBase、Spark、Hive後，想必大家對這些工具有更近一步的了解。
接下來要介紹幾個這些工具的使用情境，讓大家可以更了解使用時機。

## 純檔案模式

假設大量的原始資料都是以檔案形式存放，而且不會修改原始檔案，並且會需要使用這些檔案進行運算，這樣很適合使用Hadoop。
Hadoop的 HDFS、Yarn 與 MapReduce 符合這些使用情境，而且Hadoop的HA機制可以降低機器故障時，造成整個叢集無法使用情況。
副本(replication)機制可以降低機器故障時，不會發生資料遺失的窘境。

以副本數預設值3來說，除非很衰剛好故障的電腦都是某檔案的同block，否則HDFS是可以確保資料的可用性！
建議副本數應視叢集機器數量調整，當機器少於三台時建議將副本數調整成等於datanode的數量，而高於三台時，可以依比例調整。
例如，100台機器的叢集，可以將副本數調整成10，這樣至少可以確保同個檔案在其他9台的block損壞時，檔案還是可用的。但是這樣硬碟的容量就會升高，可用的空間就會降低，這就要看使用者如何取捨。

使用Hadoop存放檔案並需要對檔案進行運算或是萃取資料時，可以針對不同類型的使用者搭配不同運算工具來達到最好的使用模式。
如果你是：

* 1.不會寫程式與不懂SQL語法。
* 2.不會撰寫程式，會使用SQL語法。
* 3.會撰寫Java或是Scala，會使用SQL語法。

第一類的使用者，如果自己本身是資料科學家，建議與第二類或是第三類人員進行討論並委託開發，將想研究的資料透過程式視覺化後再進行研究。

第二類的使用者，懂的使用SQL則可以透過Hive CLI來查詢想觀察的資料，如果查詢邏輯過於複雜導致MapReduce的運算時間過長，則可以使用Hive On Spark的功能來加快運算效能。

第三類的使用者，彈性又更大了，除了可以使用第二類使用者的方式外，還可以透過撰寫程式碼，撰寫Spark RDD或者Spark SQL
來讀取HDFS的檔案進行資料分析，或者透過JDBC呼叫hiveserver2來執行SQL語法，查詢預先定義好的Hive table的資料。

## 資料庫模式

很多人都會有個疑問："巨量資料以檔案格式存放比較好還是存入類似RDB的儲存架構內呢？"。
首先思考一件事情，資料會不會有經常性修改的需求？又或者需不需要 Random accesses功能？
如果需要，那可以考慮將資料存放在HBase，透過預先設計好的Row key，除了加快資料查詢的速度外，也可以避免資料歪斜以平衡HBase叢集的資源使用。

與**純檔案模式**一樣分為三類使用者：

第一類使用者一樣只能透過與第二、第三類使用者溝通，幫忙把自己要的資料取出後開始解讀，如果能朝向第二、第三類的使用者邁進，那是最好的！

第二類使用者一樣可以透過使用Hive預先對HBase建立好的external table後進行查詢，但是當資料量過大也會花費更多時間，一樣可以使用 Hive On Spark的功能來加速，不過有可能會因為map數量過多而造成HBase RPC請求過多而發生timeout exception。或者也可以使用前面介紹過的HareDB HBase Clint，具有Web的使用者介面，讓使用者不必著寫程式可以直接執行SQL查詢，而且免安裝一鍵即可使用。

第三類使用者與檔案模式相同，彈性一樣很大，除了透過JDBC呼叫hiveserver2來執行SQL語法，查詢預先定義好的Hive 上的 HBase external table的資料，也可以使用HBase 的 [Spark Module][hbase_spark]來撰寫RDD。又或是透過Apache Phoenix提供的Api，在透過Phoenix建置好的table上進行資料查詢。

# 最後

不同的使用者，有不同的使用方式與情境，只有用了對的方法與工具才能讓自己在分析資料時能夠如虎添翼，
如何用對的工具與法方就要透過瞭解工具的特性才能知道，工欲善其事，必先利其器。
希望大家都可以在自己能力範圍內找到適合自己的工具。

[hbase_spark]: https://hbase.apache.org/book.html#spark

