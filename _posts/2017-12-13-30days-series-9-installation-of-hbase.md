---
layout:     post
title:      "30天系列 Day 9-HBase 安裝教學"
subtitle:   "30 Days Series Day 9 - Installation of HBase."
date:       2017-12-13 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Day 9 - Hadoop Ecosystem 之 HBase 安裝教學

HBase安裝模式與Hadoop相同，有`Standalone`、`Pseudo-Distributed`與`Fully-Distributed`，而這些名稱也是根據Hadoop的安裝模式所命名。比較特別的是`Standalone`與`Pseudo-Distributed`模式不一定要將資料儲存在HDFS上，但無法擁有Hadoop的容錯機制與分散式等優點，而`Fully-Distributed` **__只能__** 運行在Hadoop上面。

## 事前準備

與Hadoop相同，HBase原始碼也是由Java撰寫，所以也是需要安裝JDK並且設定JAVA_HOME。    
另外，HBase是架設在Hadoop上面的DataBase，如果想擁有Hadoop分散式儲存的功能，一個Hadoop叢集也是必要的。    
所以需要作下列的準備：
* 安裝JDK，並設並JAVA_HOME
* 一個Hadoop叢集（optional）
* 下載HBase tar file後解壓縮，並設定`HBASE_HOME`。這邊使用[2.0.0-appha4][hbase_2_0_0alpha4_download]版本當作安裝教學範例。    
**備註**：HBase2.0.x只支援Hadoop 2.6.1+與2.7.1+版本，其他版本目前尚未支援。
```bash
#下載已經Build好的Hadoop檔案儲存至/opt，並且解壓縮至/opt/hbase目錄
cd /opt
sudo wget http://apache.stu.edu.tw/hbase/2.0.0-alpha4/hbase-2.0.0-alpha4-bin.tar.gz
#解壓縮
sudo tar -zxvf hbase-2.0.0-alpha4-bin.tar.gz
#移動至/opt/hbase
sudo mv /opt/hbase-2.0.0-alpha4-bin /opt/hbase
```

* 設定HBASE_HOME環境變數：    
```bash
#編輯~/.bashrc
sudo vi ~/.bashrc
#加入下列參數
export HBASE_HOME=/opt/hbase
export PATH=$PATH:$HBASE_HOME/bin
```

## Standalone

與Hadoop相同，Standalone是HBase預設的模式，啟動時會在一個JVM內運行所有HBase全部的daemon，包含Master、RegionServer與ZooKeeper。    

* 使用local file system安裝模式：
  * 修改_hbase-site.xml_即可。    
  ```xml
  <configuration>
    <property>
      <name>hbase.rootdir</name>
      <value>file:///opt/data/hbase</value>
    </property>
    <property>
      <name>hbase.zookeeper.property.dataDir</name>
      <value>/opt/data/zookeeper</value>
      <description>如果沒有設定，預設路徑為/tmp</description>
    </property>
  </configuration>
  ```
  _hbase-site.xml_ 所設定的路徑如果不存在，HBase啟動時會自動建立。  

  * 啟動HBase：
  ```bash
  cd ${HBASE_HOME}/bin
  sudo bash start-hbase.sh
  ```
  如果啟動成功，可以使用`jps`指令來觀察HBase的daemon，其中會包含：
    * HMaster
    * HRegionServer
    * QuorumPeerMain
* 使用HDFS安裝模式：
  * 修改_hbase-site.xml_即可，啟動方式與`local file system`模式相同，只要將`hbase.rootdir`改為HDFS上的路徑，並將`hbase.cluster.distributed`設定為`false`
  ```xml
  <property>
      <name>hbase.rootdir</name>
      <value>hdfs://{hostname}:9000/hbase</value>
  </property>
  <property>
      <name>hbase.cluster.distributed</name>
      <value>false</value>
  </property>
  ```
  備註：`{hostname}`為主機名稱，請依照自己的Hadoop Namenode所在的hostname修改。

---

## Pseudo-Distributed

與Standalone運作方式類似，差別在於Pseudo-Distributed的daemon是在不同的JVM運作。儲存模式也與Standalone一樣，可以使用local file system或是HDFS，設定方法也與Standalone模式相同，只要修改**hbase-site.xml**。

設定為Pseudo-Distributed模式：
* 修改_hbase-site.xml_，加入下列參數設定。
  ```xml
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  ```
如果已經啟動Standalone模式的HBase，**務必請先關閉再重新啟動**，可使用下列指令關閉：
```bash
cd ${HBASE_HOME}/bin
#關閉HBase
sudo bash stop-hbase.sh
```
或者是使用rolling restart重新啟動HBase，不必完全關閉HBase。
```bash
cd ${HBASE_HOME}/bin
#rolling restart HBase
sudo bash rolling-restart.sh
```

---

## Fully-Distributed

由於`Fully-Distributed`只支援運作在HDFS，所以必須先準備好Hadoop，Hadoop運作模式一樣可以使用那三種模式。如果是要在production環境運作，無論是Hadoop或是HBase皆強烈建議使用`Fully-Distributed`。

* 設定hbase-site.xml：由於Fully-Distributed只支援運作在HDFS上，所以`hbase.rootdir`必需設定為HDFS路徑，且`hbase.cluster.distributed`需設定為`true`。HBase運作時需要zookeeper的協助，在production環境強烈建議需要一組不同於Hadoop與HBase的機器來運作zookeeper。實驗性質則可以使用HBase內建的zookeeper，不需要額外安裝。
* 設定regionservers：與Pseudo-Distributed最大不同點在於，Fully-Distributed會有多台機器運作，故需要設定此檔案讓HMaster知道HBase的成員有哪些。
```bash
#編輯regionservers
sudo vi ${HBASE_HOME}/conf/regionservers
#加入機器的hostname，如果有三台機器，請依序加入如下所示
server-a1
server-a2
server-a3
```

設定完成就可以啟動HBase了！

---

## 最後

無論是`Standalone`、`Pseudo-Distributed`或是`Fully-Distributed`，需挑選適用的安裝模式才能發揮HBase最好的效益，也能減少使用上的困擾。

介紹完了HBase安裝後，接下來要進入`hbase shell`的使用教學！

[hbase_2_0_0alpha4_download]: http://apache.stu.edu.tw/hbase/2.0.0-alpha4/
