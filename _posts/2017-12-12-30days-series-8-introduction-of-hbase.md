---
layout:     post
title:      "30天系列 Day 8-HBase 簡介"
subtitle:   "30 Days Series Day 8 - Introduction of HBase."
date:       2017-12-12 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Day 8 - Hadoop Ecosystem 之 HBase 簡介

HBase是運作在 HDFS 之上的非關連式分散式資料庫 (non-relational, distributed database)。想在 Big Data 的世界使用 DataBase，又想擁有 Hadoop 的容錯機制優點，選 HBase 就對了！HBase 具備高吞吐量與低延遲性的特點，非常適合在 Big Data進行更快速的讀寫操作。目前HBase深受各大企業或是網站使用，包含：Adobe、Facebook、Meetup、Trend Micro、Twitter、Yahoo!等等。

已經有Hadoop可以放儲存超大檔案，並且使用分散式運算MapReduce，那還需要HBase幹嘛呢？    
這是一個好問題！讓我們來回顧一下前面的Hadoop操作，有`put`, `get`, `cat`, `mv`, `rmr`等語法，有沒有發現少了什麼？沒錯！就是**edit**。    

**HBase的特點就是提供即時隨機讀寫(random, realtime read/write access)的功能。當A電腦(client)新增了一筆資料時，B電腦(client)就可以馬上讀取到最新資料並且可以修改。**

這有很厲害嗎？一般RDB都可以做到啊！沒錯，RDB在這方面的確做得到甚至更好，但是RDB無法存放PB等級以上的資料啊!(笑) 又或者Oracle DB可能可以放這麼大的資料量，但不敢想像Oracle會收多少取授權費用了...

---
### HBase 與 RDB (Relational Database)

HBase這麼厲害，可以直接取代RDB嗎？雖然HBase經過社群的努力下效能越來越好，但在某些情境還是無法完全取代RDB，他與RDB間沒有誰最好，甚至某些狀況是相輔相成。比較常見的情境是利用RDB當作HBase的metadata儲放處，或者是當作secondary index，較為複雜的relation先使用RDB查詢存在放HBase的資料主鍵(PK)，再使用PK從HBase取出raw data。

---
### HBase 與 Rowkey

如果還記得MapReduce的WordCount範例，可以了解在檔案格式中要搜尋某個關鍵字或是某行紀錄時，需要對單一或是多個檔案進行fully scan(完全掃描)後才能得到符合關鍵字的資訊，需要不少的時間來完成這件事。    

HBase是一個column-oriented的鍵值配對(**key-value** pair)的database，資料儲存以key-value方式儲存，key即是資料的主鍵(PK)也是index，稱之為Rowkey，如果善用Rowkey當作查詢條件來搜尋，效能就會比fully scan方式快上很多，這也是HBase的優勢之一，所以在存放資料時Rowkey設計就成了一們重要課題。好的Rowkey設計帶你上天堂，不好的就讓你住進refactor的套房。    

如果以RDB的table來想像，HBase的table就是一張稀疏矩陣（Sparse matrix）的表格，每個row的column數量不一定會相等，但是每個column的row數量一定會相同。

### HBase 與 大批資料

HBase以key-value方式儲存，也以key-value輸入，當需要新增大批資料時這種方法效率就很低。因此HBase提供了bulk load的功能，讓使用者不經過put機制，直接將資料轉換成HBase的儲存格式：`HFile`，避免使用put時觸發`split`與`compact`讓put效能越來越慢。

### HBase 與 SQL

基本上HBase是沒有內建任何的SQL查詢功能，只能在`hbase shell`使用`scan`搭配`filter` 查詢資料。別擔心，以SQL為功能的side project頗多，像是有名的[Apache Phoenix][phoenix_official]，或者是[Apache Trafodion][trafodion_official]，與台灣自行研發的[HareDB HBase Client][haredbhbaseclient_official]等等，都可以透過SQL語法查詢HBase的資料。

### 最後

綜合本篇的結論，接下來的篇章會為大家帶來下列有關HBase的相關介紹：
* 安裝教學
* HBase shell使用教學
* HBase hello world api教學
* Rowkey設計
* Bulk-load教學
* SQL 工具介紹

[phoenix_official]: http://phoenix.apache.org/
[trafodion_official]: http://trafodion.incubator.apache.org/
[haredbhbaseclient_official]: https://sourceforge.net/projects/haredbhbaseclie/
