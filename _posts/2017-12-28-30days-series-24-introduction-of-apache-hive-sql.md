---
layout:     post
title:      "30天系列 Day 24-Apache Hive SQL 基礎教學"
subtitle:   "30 Days Series Day 24 - Introduction Of Apache Hive SQL"
date:       2017-12-28 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

今天要來介紹Hive SQL語法基礎教學。大部分的人使用情境會是這樣：建立一個table，新增資料，進行查詢、更新資料，必要時刪除table。

首先我們必須進入Hive CLI互動式介面。

```bash
hive
```

## 建立資料表

假設我們在HDFS路徑`hdfs://{hostname}:9000/tmp/person`上有一個CSV檔案`person.csv`，資料格式如下：

```csv
Mike,Jason,+88612345678,33
Hive,Apache,+88615465788,27
Spark,Apache,+88612348775,19
HBase,Apache,+88655788444,22
Hadoop,Apache,+88987654323,34
Tomcat,Apache,+88611004322,35
...
Mary,Jean,+88612345678,60
```

一共四個欄位，分別是:`名`，`姓`，`電話`，`年齡`。接下來用下面的語法來建立hive table。

```bash
#新增資料表
create table person (first_name String,sec_name String,tel String,age int);
#由HDFS讀取檔案
load data inpath '/tmp/person/person.csv' into table person;
```

但是上面的語法在進行刪除資料表時會發生一個情形，就是連同在HDFS上的原始檔案會一並被刪除，如果想保存原始檔案，可以使用下面的語法進行新增table。

```bash
create external table person (first_name String,sec_name String,tel String,age int) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '\n' LOCATION '/person';

#說明:
ROW FORMAT DELIMITED FIELDS TERMINATED BY: 欄位的分隔符號，CSV預設為','
LINES TERMINATED BY                      : 每個row的換行符號，CSV為'\n'
LOCATION                                 : HDFS檔案存放路徑
```

使用external語法建立table，當刪除table時原始資料檔就會被保留下來了！

## 顯示資料表定義內容

既然建立了資料表，如果想了解table schema，該如何查詢呢？可以使用下面指令：

```bash
describe person;
```

## 刪除資料表

雖然用到的機會不多，但是還是要介紹一下:D

```bash
drop table if exist person purge;

#說明
if exist: 這個非必填項目，但建議使用避免輸入錯誤的table時回傳錯誤訊息。
purge   : 如果沒有使用，table不會馬上被刪除，而是會被放入類似HDFS的垃圾桶機制一樣，等時間到才會移除。
```

## 查詢資料(select)

基本的SQL是一定有的，另外包含`join`、`group by`等等。

```bash
#取出所有欄位
select * from person;
#指定顯示欄位
select first_name, sec_name, tel, age from person;
#使用where
select * from person where age > 30 or sec_name='Apache';
```

## 新增資料(insert)

Hive的insert並不會將新資料append到原始檔案內，而是建立一個新檔案。此語法於Hive 0.14版本後開始支援。

```bash
insert into table person values 
('Jack', 'Dawson', '', '125'), 
('Rose', 'DeWitt Bukater', '', '122');
```

## 刪除資料(delete)

此語法於Hive 0.14版本後開始支援。

```bash
#刪除first_name等於Mike的資料
delete from person where first_name = 'Mike';
#刪除年齡小於20
delete from person where age < 20;
```

## 更新資料(update)

此語法於Hive 0.14版本後開始支援。

```bash
update person set age = 18, tel = '' where first_name = 'Hive';
```

# 最後

本篇只針對基本用法進行說明，Hive的SQL無論是DDL或是DML功能眾多，如有需要可至[官方文件][languagemanual]查看。看完了SQL基礎教學，接下來我們就要來看Apache Hive 與 HBase的介紹囉。

[language_manual]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual