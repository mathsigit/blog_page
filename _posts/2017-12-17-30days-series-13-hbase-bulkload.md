---
layout:     post
title:      "30天系列 Day 13-HBase Bulkload 簡介"
subtitle:   "30 Days Series Day 13 - Introduction Of HBase Bulkload."
date:       2017-12-17 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

前面介紹了使用`hbase shell`與HBase client APIs新增資料，但這兩種方法只能一次新增一組key-value的資料，假如有一批檔案數十GB甚至上百GB，一筆一筆慢慢put所需要花費的時間就無法想像了，更不用說觸發split或是compact的情況下，效能可能會更加低落。

無論是`hbase shell`或是API，使用put新增資料時，會被放入被稱為`MemStore`的記憶體內，同時寫入WAL[1]。在頻繁地寫入的使用情境，為了保護資料的完整，卻會讓寫入的效能低落。

>[1] Write-Ahead-Log，用來記錄對HBase表格所有操作的日誌，用來確保table資料不遺漏，一個RegionServer只會有一個。    

為了提升輸入資料的效能，HBase提供bulkload的功能，讓使用者可以一次將大批資料透過MapReduce的方式將資料存入。

先來看一下HBase Bulkload的Usage：
```bash
hbase org.apache.hadoop.hbase.mapreduce.ImportTsv
-Dimporttsv.columns=a,b,c
-Dimporttsv.skip.bad.lines=false
'-Dimporttsv.separator=|'
-Dimporttsv.timestamp=currentTimeAsLong
<tablename> <hdfs-inputdir>

#說明：
#必填：
-Dimporttsv.bulk.output:  使用importtsv第一階段所產生的HFile存放路徑。
tablename:                欲進行匯入資料的HBase table name。
hdfs-inputdir:            欲匯入的資料檔案路徑，必須存放在HDFS。
#選用，不一定要使用：
importtsv.columns:        HBase table 欄位名稱。如果是Rowkey則使用HBASE_ROW_KEY，需使用cf:qualifier的格式
importtsv.skip.bad.lines: 如果該行資料不符格式，跳過此行繼續讀取其他的資料，如果沒有設定為true，遇到badline整個bolkload作業會因錯誤而停止。
importtsv.separator:      檔案內每個欄位的分隔符號，預設為`,`。
importtsv.timestamp:      時間註記。
```

## HBase Bulkload 步驟

* Step 1: 上傳資料到HDFS。
假設有個檔案`test.txt`，內容如下：
```bash
row1|c1|c2
row2|c1|c2
row3|c1|c2
row4|c1|c2
row5|c1|c2
row6|c1|c2
row7|c1|c2
row8|c1|c2
row9|c1|c2
row10|c1|c2
```
上傳至HDFS路徑`/tmp/bkloadInputData/test.txt`

* Step 2: 建立HBase table:    
```bash
create 'bkloadTable', {NAME => 'cf'}
```

* Step 3: 使用ImportTsv進行Bolkload:    
假設已經安裝了Hadoop，並已經設定好環境變數，延續HBase安裝所使用的版本:2.0.0-alpha4。
```bash
hadoop jar ${HBASE_HOME}/lib/hbase-mapreduce-2.0.0-alpha4.jar importtsv -Dimporttsv.columns=HBASE_ROW_KEY,cf:column1,cf:column2
-Dimporttsv.skip.bad.lines=false
'-Dimporttsv.separator=,'
-Dimporttsv.bulk.output=hdfs://{hostname}:9000/tmp/testoutput
bkloadTable
hdfs://{hostname}:9000/tmp/bkloadInputData
```
成功執行後，可在`http://{hadoop-master}:8088`的頁面看到有個importtsv的JOB正在執行，成功執行完成後bolkload第一階段也就完成了。

* Step 4: Complete BulkLoad
執行指令：
```bash
hadoop jar ${HBASE_HOME}/lib/hbase-mapreduce-2.0.0-alpha4.jar
completebulkload
hdfs://{hostname}:9000/tmp/testoutput
bkloadTable
```
這個步驟所需要花費的時間相當短，數秒鐘就可完成。

經過Step 4 與Step 5後，test.txt的檔案內容就已經被匯入bkloadTable資料表內。匯入資料時可以搭配前面所介紹的Rowkey設計，這樣會讓HBase發揮更好的效果。接下來的篇章要來介紹HBase相關的SQL工具。
