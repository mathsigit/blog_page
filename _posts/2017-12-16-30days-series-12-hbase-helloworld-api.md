---
layout:     post
title:      "30天系列 Day 12-HBase Hello World API 教學"
subtitle:   "30 Days Series Day 12 - HBase Hello World APIs."
date:       2017-12-16 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

在這篇文章內容，將會學到如何使用HBase Client API對HBase的CRUD操作。本篇的範例程式碼所使用的HBase版本是 _HBase 0.98.x_ ，相容於 _HBase 1.x.x_ 。

首先先來看一下SQL、API與HBase Shell的CRUD對照：

SQL|HBase Client API|HBase Shell
:------:|:--------:|:--------:
select|Get/Scan|get/scan
insert|Put     |put
delete|Delete  |delete
update|Put     |get/scan

## HBase Connection

與所有RDB相同，連線到HBase也需要設定connection：

```java
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.hbase.HBaseConfiguration;
import org.apache.hadoop.hbase.client.HBaseAdmin;
import org.apache.hadoop.hbase.client.HTable;

Configuration conf = HBaseConfiguration.create();
conf.set("hbase.zookeeper.quorum", "localhost");
conf.set("hbase.zookeeper.property.clientPort", “2181");  
HBaseAdmin admin = new HBaseAdmin(conf);
String tableName = "employees";
HTable table = new HTable(conf, tableName);
```

## 使用`Put`新增一筆(row)資料

```java
String tableName = "employees";
HTable htable = new HTable(conf, tableName);

Put put = new Put(Bytes.toBytes("Development_999999"));

byte[] familyName = Bytes.toBytes("cf1");
String[] columnNames = new String[] { "p-birth_date", "p-first_name",
 "p-last_name","p-gender", "p-hire_date", "d-from_date", "d-to_date" };
String[] values = new String[] { "1970-03-10", "John", "Li", "M", "1988-05-08", "1988-05-08", "9999-01-01" };

put.add(familyName, Bytes.toBytes(columnNames[0]), Bytes.toBytes( toDateValue(values[0]) ));
put.add(familyName, Bytes.toBytes(columnNames[1]), Bytes.toBytes( values[1] ));
put.add(familyName, Bytes.toBytes(columnNames[2]), Bytes.toBytes( values[2] ));
put.add(familyName, Bytes.toBytes(columnNames[3]), Bytes.toBytes( values[3] ));
put.add(familyName, Bytes.toBytes(columnNames[4]), Bytes.toBytes( toDateValue(values[4]) ));
put.add(familyName, Bytes.toBytes(columnNames[5]), Bytes.toBytes( toDateValue(values[5]) ));
put.add(familyName, Bytes.toBytes(columnNames[6]), Bytes.toBytes( toDateValue(values[6]) ));

htable.put(put);

htable.close();
```

## 使用`Get`查詢一筆(row)資料

現在我們來查剛剛Put進去的資料。

```java
String tableName = "employees";
HTable htable = new HTable(conf, tableName);
Get get = new Get(Bytes.toBytes("Development_999999"));  
Result result = htable.get(get);

System.out.println( "result size: " + result.size() );
System.out.println( "result string: " + result.toString() );
for (int i=0; i<result.size(); i++) {
    System.out.println( String.format("result[%d]: %s", i, result.raw()[i].toString()));
}

htable.close();
```

## 使用`Scan`來查詢table所有資料

```java
String tableName = "employees";

HTable htable = new HTable(conf, tableName);
Scan scan = new Scan();
ResultScanner scanner = htable.getScanner(scan);
for (Result res : scanner) {
  System.out.println(res);
}
scanner.close();

htable.close();
```

## 使用`Delete`刪除一筆資料

```java
String tableName = "employees";

HTable htable = new HTable(conf, tableName);
Delete delete = new Delete(Bytes.toBytes("Development_999999"));  
htable.delete(delete);
htable.close();
```

# 最後

看完了Hello World版本的API後，接下來要介紹如何使用HBase Bolk-Load。
