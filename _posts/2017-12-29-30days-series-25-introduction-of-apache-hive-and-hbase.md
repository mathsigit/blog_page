---
layout:     post
title:      "30天系列 Day 24 - Apache Hive 與 HBase 整合應用介紹"
subtitle:   "30 Days Series Day 24 - Integration Of Apache Hive and Apache HBase"
date:       2017-12-28 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

今天要來介紹如何透過使用Hive來對HBase table進行查詢。由前面的HBase篇章已經知道HBase本身是沒有提供SQL查詢的介面與功能，
只能依賴其他的工具，例如 Phoenix 或是本篇要介紹的 Hive。

而由於 Hive 可以使用Hive CLI且安裝簡易，不用啟動service就可以使用，所以受到不少使用者使用，接下來就來介紹Hive如何與HBase整合吧！

## 整合設定

開始之前要注意一件事情：
> Hive 1.x版本目前只支援HBase 0.98.x與之前的版本。而HBase 1.x之後的版本，請使用Hive 2.x之後的版本。
>假設你想在Hive 1.x使用HBase 1.x，那就要自行重新編譯Hive的原始碼。

好的，假設我們已經有一個HBase叢集，且下載[Hive 2.1.1][hive211_download]版本，Hive CLI環境也已經設定完成，接著只要在進行修改`hive-site.xml`，即可透過Hive CLI使用SQL語法對HBase進行操作。

開啟`hive-site.xml`並加入下面的設定值：

```xml
<property>
    <name>hbase.zookeeper.quorum</name>
    <value>{zookeeper host}</value>
    <description>使用HBase內建的zookeeper則指定為HBase master的hostname，若是使用外部且多台zookeeper，請用,分隔。
    </description>
</property>
<property>
    <name>hive.aux.jars.path</name>
    <value>${HIVE_HOME}/lib/hive-hbase-handler-2.1.1.jar,
           ${HIVE_HOME}lib/hbase-server-1.1.1.jar,
           ${HIVE_HOME}/lib/zookeeper-3.4.6.jar,
           ${HIVE_HOME}/lib/guava-14.0.1.jar
    </value>
</property>
```

設定完畢後，重新啟動Hive CLI即可開始透過 SQL 操作HBase Table啦！

## 建立資料表

其實Hive與HBase整合非常簡單與方便，下面會介紹兩種使用情境，透過Hive CLI建立HBase table的語法，分別是：

* 透過Hive新增HBase table。
* 透過Hive如何查詢已經存在的HBase table。

### 透過Hive新增HBase table

假設HBase上面尚未有任何table，我們可以使用Hive SQL來建立一個

```bash
#啟動hive
hive
```

輸入下面語法：

```bash
CREATE TABLE new_hbase_table(key int, value string) 
STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
WITH SERDEPROPERTIES ("hbase.columns.mapping" = ":key,cf1:val")
TBLPROPERTIES ("hbase.table.name" = "new_table", "hbase.mapred.output.outputtable" = "new_table");

#說明
hbase.columns.mapping           : 欄位對應。必須與hive table的順序相對應。例如：key對應到:key，value對應到cf:value，id對應到cf:id。
hbase.table.name                : HBase table name。若沒有指定，預設hive table name會與hbase table name相同。
hbase.mapred.output.outputtable : HBase table name，非必要設定。若需要在Hive使用insert的指令，則需要指定。
```

完成後可以使用`hbase shell`進入查詢，此時可以發現new_table已經被建立，要注意的是，透過這個語法所建立的HBase table，當在Hive CLI使用drop table指令時，HBase table同時會被刪除。

### 透過Hive如何查詢已經存在的HBase table

只要進入Hive CLI後，對已經存在的HBase table 建立Hive external table，定義欄位與型別即可。

假設我們有一個HBase table `hbase_table`，並且使用`hbase shell`的`put`指令新增資料:

```bash
put 'hbase_table', 'rk_1', 'cf:value', '65535'
put 'hbase_table', 'rk_2', 'cf:value', '111'
put 'hbase_table', 'rk_3', 'cf:value', '9487'
put 'hbase_table', 'rk_3', 'cf:id', 'John'
```

可以看出來這個HBase table的擁有column family: `cf`。接下來在Hive CLI內使用下面的指令：

```bash
#啟動hive
hive
```

```bash
#Create Hive table on HBase
create external table hbase_table(key string, value string, id string)
stored by 'org.apache.hadoop.hive.hbase.hbasestoragehandler'
with serdeproperties ("hbase.columns.mapping" = ":key,cf:value,cf:id") tblproperties ("hbase.table.name" = "hbase_table" "hbase.mapred.output.outputtable" = "hbase_table");
```

當hive table建立完畢，該資料表會長成這樣：

key|id|value
----|--------|--------
rk_1|    |65535
rk_2|    |111
rk_3|John|9487

透過`create external table`指令建立的hive table，當使用drop table指令時，原本的HBase table是不會被刪除的。

## 使用

當hive table建立完成後，我們就可以透過hive sql語法來對HBase進行操作，語法使用可以參考"Apache Hive SQL 基礎教學"這一篇文章。要注意的是，透過hive sql對HBase進行查詢時，若是使用join語法，會觸發mapreduce的job執行，當HBase資料量龐大region數量很多時，mapreduce運作時會開啟大量map與HBase進行溝通，此時HBase會受到大量的RPC連線，有機會影響到HBase其他的AP的運作，甚至導致HBase regionserver shutdown ，請務必小心使用。

# 最後

看完了Hive 與 HBase的整合應用教學，接下來要來介紹如何使用hiveserver2囉。


[hive211_download]: http://apache.stu.edu.tw/hive/hive-2.1.1/