---
layout:     post
title:      "30天系列 Day 19-Apache Spark Submit 簡介"
subtitle:   "30 Days Series Day 19 - Introduction Of Apache Spark Submit"
date:       2017-12-23 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Spark Submit

Spark submit 是Spark用來送出程式到叢集執行的script。目前支援的叢集平台/模式有下列幾種：
* Standalone
  * Spark Standalone 模式
* Apache Mesos
* Hadoop YARN
* Kubernetes 
  * 目前在Spark 2.2以上開始支援，目前還在測試階段。


## 打包程式吧！

為了讓`spark-submit`的script可以順利將程式碼送出執行，除了Python以外，Scala、Java都需要將程式碼編譯並打包成jar，可以使用 Sbt 或是 Maven 來幫忙進行複雜的 dependency 管理與打包。

如果是Python，使用spark-submit時需要使用`--py-files`指定要執行的.py、.zip或是.egg檔案。建議若是要執行的python檔案有一個以上，可以將他們打包成.zip或是.egg後再送出。

## Spark Submit Usage

接下來看一下spark-submit的指令與參數：

```bash
./bin/spark-submit \
  --class <main-class> \
  --master <master-url> \
  --deploy-mode <deploy-mode> \
  --conf <key>=<value> \
  <application-jar> \
  [application-arguments]
```

參數說明：
* --class: 主程式的進入點，例如:org.apache.spark.examples.SparkPi。
* --master: 要執行Spark程式叢集/平台的master url，下面會介紹目前可使用的格式。
* --deploy-mode: 程式的driver要在哪邊執行。`client` 或是 `cluster`，預設為`client`。假設程式是不需要回傳值，可以使用cluster，如果需要回傳計算結果，那需要使用client，這樣執行spark-submit的視窗才能顯示計算結果。需要注意的是，目前spark standalone**不支援**在Python程式使用cluster模式來進行。
* --conf: 各種Spark參數設定都可以用key=value的格式傳入
* application-jar: 要執行的spark程式(jar)路徑。檔案路徑可以放在HDFS、 本機路徑或是某個http url，但必須帶入指定路徑讓spark-submit可以辨識：`hdfs://`、`file://`、`http://`。如果是python檔案，這個參數需要以` --py-files`替換。
* application-arguments: 傳遞參數讓主程式使用。

**Master URLs:**

Master URL | Meaning
---|---
local      |使用單一執行緒(thread)在本機執行
local[K]   |使用K個執行緒(thread)在本機執行。理想狀況上可以設定單一機器cpu core數量的上限。
local[*]   |使用與本機cpu core數量相同的執行緒(thread)在本機執行。
spark://HOST:PORT |連接到Spark Standalone叢集的master node，預設PORT為7070。
mesos://HOST:PORT |連接到Apache Mesos叢集上運行，預設PORT為5050。
yarn             |連接到Hadoop YARN的叢集進行運算。首先會根據`HADOOP_CONF_DIR`或`YARN_CONF_DIR`路徑內的設定檔知道yarn叢集的位址(可以搭配Day 4 Hadoop安裝篇章來複習)，再判斷--deploy-mode是client或是cluster模式來運作。

接下來看一些spark-submit的範例：

```bash
# Execute SparkPi locally with 6 cores
./bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master local[6] \
  /path/to/spark-examples.jar \

# Execute on a YARN cluster
export HADOOP_CONF_DIR=XXX
./bin/spark-submit \
  --class org.apache.spark.examples.SparkPi \
  --master yarn \
  --deploy-mode cluster \  # can be client for client mode
  /path/to/spark-examples.jar \

# Execute Python application on Spark standalone
./bin/spark-submit \
  --master spark://172.124.161.138:7077 \
  examples/src/main/python/pi.py \
```

# 最後

看完了spark-submit的介紹後，想必對執行Spark的應用程式的指令有更近一步了瞭解。接下來要來介紹Spark的另一個重要模組`Spark Sql`