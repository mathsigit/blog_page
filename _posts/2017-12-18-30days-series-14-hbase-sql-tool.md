---
layout:     post
title:      "30天系列 Day 14-HBase SQL工具介紹"
subtitle:   "30 Days Series Day 14 - Introduction Of HBase SQL Tools."
date:       2017-12-18 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

在前面的介紹篇章有提到，HBase本身並沒有內建SQL查詢的介面讓使用者查詢。
但並不因為這樣就無法使用SQL語法對HBase進行查詢。這篇要來介紹幾個基於HBase可使用SQL查詢的工具。

## Apache Phoenix

Phoenix是基於HBase所開發出來的數據查詢層，使用者可以透過JDBC向Query execution engine使用ANSI SQL語法進行查詢，Phoenix會將SQL語法轉換成各種Scan與Filter的HBase Client API後，並使用預先在HBase內註冊的`Coprocessor`對HBase進行分散式查詢。    

由於是透過HBase Coprocessor的方法進行查詢，反應時間最快可以在毫秒等級，是目前HBase SQL查詢的推薦工具之一。使用者只要透過撰寫JDBC的程式即可使用符合ANSI SQL的語法對HBase進行資料的操作！除此之外還具有ACID功能讓使用者可以更近一步的使用SQL對HBase進行更近一步的操作。

不過使用Phoenix後HBase的 raw data有可能會被修改成Phoenix制訂的格式，而原先使用HBase scan可認得的資料可能就無法辨識！

Phoenix原先由Saleforce所開發，爾後捐獻給Apache基金會，後來變成Apache Top project。

相關安裝教學請看[官方網站][phoenix_official]

![phoenix.png](../../../../img/30_days/phoenix.png)    
From:https://phoenix.apache.org/presentations/OC-HUG-2014-10-4x3.pdf

## HareDB HBase Client

由台灣的軟體公司[亦思科技][is_land]所研發，結合Apache Hive與HBase Coprocessor的特點，除了擁有媲美Apache Phoenix的查詢效能外，一鍵即可使用、免安裝，讓使用者可以透過內建的Web介面來對HBase進行資料的操作。內建支援HBase Bulkload功能，可讓使用者在Web介面設定資訊後，一鍵送出即可，並可透過監控畫面進行Job的觀察。

其最新版本1.120.04s支援HBase 1.12版本，並內建Hive on Spark功能，可對儲存於HDFS上的格式化的資料，使用Hive SQL透過Spark的分散式運算進行查詢，效能比MapReduce快上許多！

免費版本可至[haredbhbaseclient_sourceforge][haredbhbaseclient_official]，或者到[HareDB官方網站][haredbhbaseclient_official]下載。    
60天完整試用版經過Cloudera官方認證，具有Security功能。

[phoenix_official]: http://phoenix.apache.org/
[is_land]: https://www.is-land.com.tw
[haredbhbaseclient_sourceforge]: https://sourceforge.net/projects/haredbhbaseclie/files/
[haredbhbaseclient_official]: http://www.haredb.com/HareDB/src_ap/Download_HBaseClient.aspx?l=4

簡單介紹完了HBase 的SQL查詢工具後，HBase的介紹也告一段落，接下來的篇章要來介紹Apache Spark。
