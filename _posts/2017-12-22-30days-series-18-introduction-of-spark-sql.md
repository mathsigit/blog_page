---
layout:     post
title:      "30天系列 Day 20-Apache Spark SQL 簡介"
subtitle:   "30 Days Series Day 20 - Introduction Of Apache Spark SQL"
date:       2017-12-24 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Spark SQL

Spark SQL是Spark用來執行SQL語法查詢的一種功能，也支援HiveQL查詢語法，可透過Spark application 撰寫程式對使用Hive建立的Table進行查詢，可以透過`.{$SPARK_HOME}/bin/spark-sql`在command模式或透過hiverserver2使用JDBC/ODBC來查詢Hive table。

# DataFrame

使用Spark SQL回傳的物件類型是DataFrame，是一種用來命名欄位的分散式資料集合。
它的概念有點像優化版本的RDB table，可以接受更多的資料來源建立DataFrame，例如結構化的的檔案(CSV)、Hive Table、外部資料庫或是Spark RDD。與RDD一樣，DataFrame目前有支援下列的語言：

* Scala
* Java
* Python
* R

# DataSet

DataSet在Spark 1.6版本所提出，想藉由Spark SQL的優化引擎來強化RDD的優勢，可以想像成是加強版的DataFrame。DataSet提供強型別(strong type)與lambda functions。其中強型別的好處是在編譯時就可以發現資料型態是否正確而提出警告，有別於傳統RDD必須要在執行期(runtime)才能發現，可以儘早改善錯誤。

# Sample Code

現在開始來示範一個sample code進行操作Spark SQL，下面程式碼是以spark 2.1.2所撰寫。

```Java
import org.apache.spark.sql.SparkSession
import spark.implicits._

/*
 * SparkSession在Spark 2.0提出，未來計畫取代SparkContext。
 * SparkSession包含了Saprk SQL、Streaming等等元件的進入點，並且支援查詢Hive包含HiveQL或UDF等等。
 */
val spark = SparkSession
  .builder()
  .appName("Spark SQL basic example")
  .config("spark.some.config.option", "some-value")
  .getOrCreate()

//建立一個DataFrame
val df = spark.read.json("examples/src/main/resources/people.json")

// 顯示DataFrame資料內容：
df.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+

// 印出DataFrame的schema結構
df.printSchema()
// root
// |-- age: long (nullable = true)
// |-- name: string (nullable = true)

// 只顯示"name"欄位
df.select("name").show()
// +-------+
// |   name|
// +-------+
// |Michael|
// |   Andy|
// | Justin|
// +-------+

// 選出"name"、"age"，並且將age欄位的值+1
df.select($"name", $"age" + 1).show()
// +-------+---------+
// |   name|(age + 1)|
// +-------+---------+
// |Michael|     null|
// |   Andy|       31|
// | Justin|       20|
// +-------+---------+

// 選出age欄位值大於25的資料
df.filter($"age" > 25).show()
// +---+----+
// |age|name|
// +---+----+
// | 30|Andy|
// +---+----+

// 以name分群並計算數量
df.groupBy("age").count().show()
// +----+-----+
// | age|count|
// +----+-----+
// |  19|    1|
// |null|    1|
// |  30|    1|
// +----+-----+


//使用Spark SQL進行查詢
// 首先註冊一個DataFrame當作SQL語法會用到的暫存veiw："employee"
df.createOrReplaceTempView("employee")

val sqlDF = spark.sql("SELECT * FROM employee")
sqlDF.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+

//使用DataSet
// 注意： case class 在 Scala 2.10 最多只支援22個欄位，請注意這個限制。
case class Person(name: String, age: Long)

// 將case clasee 進行編碼後再轉換成DataSet
val caseClassDS = Seq(Person("Andy", 32)).toDS()
caseClassDS.show()
// +----+---+
// |name|age|
// +----+---+
// |Andy| 32|
// +----+---+

// DataFrames 可以透過指定case class轉換成DataSet。
val path = "examples/src/main/resources/people.json"
val peopleDS = spark.read.json(path).as[Person]
peopleDS.show()
// +----+-------+
// | age|   name|
// +----+-------+
// |null|Michael|
// |  30|   Andy|
// |  19| Justin|
// +----+-------+
```

# 最後

介紹完了基本的Spark SQL與DataDrame、DataSet後，應該可以更了解Spark SQL對於查詢結構化的資料是相當便利的，那如果想對HBase使用Spark SQL呢？沒問題的！經過HBase社群的努力，也開發出了支援Spark的模組，詳情可以到[HBase and Spark][hbase_and_spark]來查看更詳細的內容！

看完了Spark SQL，接下來就要來介紹Spark Stream囉！

[hbase_and_spark]: https://hbase.apache.org/book.html#spark