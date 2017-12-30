---
layout:     post
title:      "30天系列 Day 23-Apache Hive 安裝教學"
subtitle:   "30 Days Series Day 23 - Installation Of Apache Hive"
date:       2017-12-27 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

今天要介紹 Hadoop Ecosystem 中火力威猛的Data Warehouse工具 - Apache Hive 的安裝教學。

前面有提到Apache Hive主要是透過對儲存在HDFS上的結構化資料定義Schema後，以SQL佐以Schema來查詢"**檔案**"。
而Apache Hive 的 Schema需要某個儲存裝置來存放這些資料，通常是使用RDB，如果沒有任何的RDB service也可以使用Hive內建的 embedded DB(Derby)。

## 事前準備

* 安裝Java
  * 建議使用Java 1.8以上。基本上Hive 1.2之後的版本只支援Java 1.7以上的版本。
* 一個Hadoop叢集，版本至少要2.x以上。
  * 如果有根據前面的教學安裝，現在應該會有一個 Hadoop 2.7.3 的叢集可以使用。
* Mysql(非必要)。

## Hive安裝教學

下載Hive 編譯好的檔案，可由[Hive官方的連結][hive_download]下載。本篇教學會使用1.2.2版本。
下載完畢使用下面指令進行解壓縮：

```bash
tar -xzvf apache-hive-1.2.2-bin.tar.gz
```

解壓縮完畢後，編輯`~/.bashrc`設定HIVE_HOME環境變數。

```bash
export HIVE_HOME=/path/to/your/hive
export PATH=$HIVE_HOME/bin:$PATH
```

設定`hive-site.xml`檔案：

```xml
<configuration>
    <property>
        <name>fs.default.name</name>
        <value>hdfs://{hostname}:9000</value>
        <description>For YARN this configuration variable is called fs.defaultFS.</description>
    </property>
</configuration>
```
備註：`{hostname}`為主機名稱，請依照自己的Hadoop Namenode所在的hostname修改。

設定完畢後，就可以啟動Hive CLI來與HDFS互動囉！

```bash
hive
```

## Metastore 設定

前面有介紹Hive可以將Schema儲存在RDB上，假設我們有一個mysql db，只要在`hive-site.xml`增加連線資訊的設定，就可以讓Schema儲存到我們指定的RDB內了，但必須先在mysql內建立Hive metastore的RDB schema，可以使用Hive內建script來建立：

```bash
#使用root角色匯入schema，如果沒有root角色權限，請使用有權限之使用者。
mysql -uroot -e "CREATE DATABASE IF NOT EXISTS metastore_db"
cd ${HIVE_HOME}/scripts/metastore/upgrade/mysql
mysql -u root metastore_db<hive-schema-1.2.0.mysql.sql
```

匯入完畢，接著增加下列設定到`hive-site.xml`：

```xml
<property>
    <name>javax.jdo.option.ConnectionURL</name>
    <value>jdbc:mysql://{hostname}/metastore_db</value>
</property>

<property>
    <name>javax.jdo.option.ConnectionDriverName</name>
    <value>com.mysql.jdbc.Driver</value>
</property>

<property>
    <name>javax.jdo.option.ConnectionUserName</name>
    <value>root</value>
</property>

<property>
    <name>javax.jdo.option.ConnectionPassword</name>
    <value>123456</value>
</property>
```
備註：`{hostname}`為主機名稱，請依照自己的mysql所在的hostname修改。

接下來要到mysql官方網站下載[mysql JDBC connector][mysql_jdbc_connector]

好的！這樣就設定完成了！Hive CLI僅提供單機運作，如果想以server形式透過JDBC讓多人同時連線進來操作，可以透過啟動`hiveserver2` service讓多人同時連線操作。

```bash
#啟動hiveserver2
hive --service hiveserver2
```

[hive_download]: http://apache.stu.edu.tw/hive/
[mysql_jdbc_connector]: https://dev.mysql.com/downloads/connector/j/