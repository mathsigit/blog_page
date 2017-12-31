---
layout:     post
title:      "30天系列 Day 27-Apache Hive Server On Spark簡介"
subtitle:   "30 Days Series Day 27 - Introduction Of Apache Hive On Spark"
date:       2017-12-31 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

使用複雜的Hive SQL語法查詢時，預設狀況下是使用MapReduce來進行運算。自從Apache Spark出現之後，MapReduce已經有逐漸被取代的跡象。
Apache Hive在1.1版本推出Hive On Spark功能，透過Spark高效能計算速度的優點，讓使用者有更好的使用者體驗。

## 前置作業

一般而言，大家會想說`Hive On Spark`，那我只要到Apache Spark網站下載build好的jar檔案，放入Hive的安裝環境下這應應該就可以跑了吧！
沒錯！理論上是這樣，但是**代志恩洗憨人想A嫁擬甘單**，Apache Spark有個功能整合Hive，可以讓Spark Application以RDD或者
是Spark SQL的方式讀取Hive table，Apache Spark官網提供的pre-build Spark檔案，已經包含Hive的相關dependency檔案。

如果在Hive上使用官網下載的Spark jar檔來使用Hive On Spark的功能，這樣是會發生Jar衝突的錯誤訊息的！所以必須重新打包Apache Spark 的source code，並將Hive的dependency排除。

### Build Apache Without Hive

重新打包Spark source code可以透過三種方式：`SBT`、`Maven`或者是`make-distribution.sh`。
使用SBT或是Maven這兩種方法打包出來的spark-assembly檔案會比較大，在部署上會花費較多時間，故建議使用**make-distribution.sh**。
這裡也只示範如何使用make-distribution.sh來打包。首先請到官方網站下載[Spark 2.1.2的原始碼][Spark_212_source]，並執行下列語法：

```bash
#make-distribution.sh
.${SPARK_HOME}/dev/make-distribution.sh --name "hadoop2-without-hive" --tgz "-Pyarn,hadoop-provided,hadoop-2.7"
```

由於前面介紹Hadoop時，是使用Hadoop 2.7.3版本，因此打包時也使用hadoop-2.7的參數。

## 設定Hive

這裡使用Hive 2.1.1版本，只要將打包好的spark-assembly檔案放入`$HIVE_HOME/lib`內即可。Hive 2.2.0(含)之後的版本，Hive只支援Spark 2.0及之後的新版本，詳細安裝方式可參考[Hive官方網站][config_hive_on_spark]。

## 啟動Hive On Spark

進入Hive CLI後輸入下面的設定，即可啟用Hive On Spark:

```bash
#進入Hive CEL
hive

#設定使用spark engine
set hive.execution.engine=spark;
```

接下來就可以進行查詢，而原本使用MapReduce進行運算的Hive SQL語法也會變成Spark進行運算，可以在如果是使用yarn當作運算資源管理系統，可以透過`http://{hostname}:8088`觀察Hive送出的application。

# 最後

簡介完了Hive On Spark，Hive篇章也到了最後。接下來兩天要來介紹，目前這些Hadoop ecosystem 整合應用的use case。

[Spark_212_source]: https://www.apache.org/dyn/closer.lua/spark/spark-2.1.2/spark-2.1.2.tgz
[config_hive_on_spark]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Spark%3A+Getting+Started#HiveonSpark:GettingStarted-ConfiguringHive