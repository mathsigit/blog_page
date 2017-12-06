---
layout:     post
title:      "30天系列 Day 3-Hadoop安裝"
subtitle:   "30 Days Series Day 3 - Installation of Hadoop."
date:       2017-12-06 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Day 3 - Hadoop Ecosystem 之 Hadoop 安裝

前面的簡介中有提到，Hadoop是個分散式儲存及運算的架構，所以安裝基本上需要多台電腦以達到分散的目標（使用VM或是Container技術，如VirtualBox、Docker、K8s等，在大型叢集上會產生另外的問題，故暫時不列入討論）。但Hadoop為了讓使用者可以快速地開始使用，提供單一主機的安裝模式共有兩種模式：`獨立模式(Standalone)`與`偽分佈模式(Pseudo-Distributed)`。多機安裝模式則稱為`Fully-Distributed`。

## 在安裝之前需要先做好下列的事前準備：
1. 一台安裝好Linux作業系統(作者使用的是CentOS 6.6)的PC。
  * Hadoop支援Linux及Windows作業系統(Hadoop 2.2版本之後開始支援Windows)，Hadoop官網所下載的檔案只支援Linux，這篇教學也以Linux為主。若需要安裝在Windows則需要下載Hadoop原始碼並自行打包，若想嘗試可參考[Build and Install Hadoop 2.x or newer on Windows](https://wiki.apache.org/hadoop/Hadoop2OnWindows)。
2. 並且安裝好Java並設定好環境變數。
  * Hadoop 2.6與更之前的版本支援Java 6，2.7版之後只支援Java 7，Hadoop 3.0版本開始只支援Java 8。此次安裝教學是使用Hadoop 2.8.2，安裝Java 7最新版本即可。下載點可至[oracle官網](http://www.oracle.com/technetwork/java/javase/downloads/java-archive-downloads-javase7-521261.html)選擇適合的OS下載。
3. 安裝下列軟體`ssh`與`rsync`：    
**CentOS:**
```bash
sudo yum install ssh
sudo yum install rsync
```
**Ubuntu:**
```bash
sudo apt-get install ssh
sudo apt-get install rsync
```
4. 從官網下載已經Build好的Hadoop檔案儲存至`/opt`，並且解壓縮至`/opt/hadoop`目錄。
```bash
sudo wget http://apache.stu.edu.tw/hadoop/common/hadoop-2.8.2/hadoop-2.8.2.tar.gz -P /opt
```
```bash
cd /opt
```
```bash
sudo tar -zxvf hadoop-2.8.2.tar.gz
```
```bash
sudo mv /opt/hadoop-2.8.2 /opt/hadoop
```
5. 設定HADOOP_HOME環境變數：    
編輯~/.bashrc
```bash
sudo vi ~/.bashrc
```
並加入下列參數
```bash
export HADOOP_HOME=/opt/hadoop
export PATH=$PATH:$HADOOP_HOME/sbin:$HADOOP_HOME/bin
```

## 開始安裝 - 獨立模式 Standalone
基本上Hadoop預設的模式就是Standalone，只要完成事前準備步驟1-5即可使用。Standalone僅供測試與體驗用途，若要執行大量運算或是儲存大量資料有可能就會撐不住啦。

讓我們來測試一下Standalone是否可以正常運作。
* 檢查Hadoop版本：    
```bash
hadoop version
```
```bash
#回傳結果：
Hadoop 2.8.2
Subversion https://git-wip-us.apache.org/repos/asf/hadoop.git -r 66c47f2a01ad9637879e95f80c41f798373828fb
Compiled by jdu on 2017-10-19T20:39Z
Compiled with protoc 2.5.0
From source with checksum dce55e5afe30c210816b39b631a53b1d
This command was run using /home/stana/hadoop-2.8.2/share/hadoop/common/hadoop-common-2.8.2.jar
```

* 嘗試在Standalone上執行mapreduce的運算：
```bash
cd ~
mkdir input
cp $HADOOP_HOME/etc/hadoop/*.xml input
hadoop jar $HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-2.8.2.jar grep input output 'dfs[a-z.]+'
```
透過上面的指令，會把`$HADOOP_HOME/etc/hadoop/*.xml`路徑下所有`.xml`的檔案複製到`~/input`內，並使用hadoop-mapreduce-examples-2.8.2.jar執行MapReduce Job，會把`~/input`內所有檔案有關_dfs_開頭的字串撈出來。    
```bash
#觀察執行結果：
cat output/*
```
可以看到符合條件的字串內容：
```bash
1	dfsadmin
```
**Standalone的File System會直接使用本機的檔案系統，而非分散式的HDFS，這一點要特別注意。**

## 開始安裝 - 偽分佈模式 Pseudo-Distributed
如果已經做好事前準備，完成解壓縮Hadoop tar檔案，接著就可以開始進行偽分佈安教步驟。
* Step 1. 編輯hadoop-env.sh。
```bash
sudo vi $HADOOP_HOME/etc/hadoop/hadoop-env.sh
```
加入JAVA_HOME設定，`{path of your jdk7}`為本機JAVA_HOME路徑：
```bash
# set to the root of your Java installation
export JAVA_HOME={path of your jdk7}
```
* Step 2. 編輯HDFS設定檔    
  * core-site.xml
  ```bash
  sudo vi $HADOOP_HOME/etc/hadoop/core-site.xml
  ```
  加入下列設定，指定本機為HDFS的路徑(預設為`file://`)：
  ```bash
  <configuration>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://localhost:9000</value>
      </property>
  </configuration>
  ```
  * 編輯hdfs-site.xml
  ```bash
  sudo vi $HADOOP_HOME/etc/hadoop/hdfs-site.xml
  ```
  加入下列設定，將副本改為1(Default:3)。由於偽分佈只有一台機器，所以副本數改為1即可：
  ```bash
  <configuration>
      <property>
          <name>dfs.replication</name>
          <value>1</value>
      </property>
  </configuration>
  ```
* Step 3. 編輯Yarn設定檔
  * mapred-site.xml
  ```bash
  sudo cp $HADOOP_HOME/etc/hadoop/mapred-site.xml.template $HADOOP_HOME/etc/hadoop/mapred-site.xml
  sudo vi $HADOOP_HOME/etc/hadoop/mapred-site.xml
  ```
  加入下列設定：
  ```bash
  <configuration>
      <property>
          <name>mapreduce.framework.name</name>
          <value>yarn</value>
      </property>
  </configuration>
  ```
  * yarn-site.xml
  ```bash
  sudo vi $HADOOP_HOME/etc/hadoop/yarn-site.xml
  ```
  加入下列設定：
  ```bash
  <configuration>
      <property>
          <name>yarn.nodemanager.aux-services</name>
          <value>mapreduce_shuffle</value>
      </property>
  </configuration>
  ```
* Step 4. 設定免密碼登入(ssh)
```bash
sudo ssh-keygen -t rsa -P '' -f ~/.ssh/id_rsa
sudo cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
sudo chmod 0600 ~/.ssh/authorized_keys
```

* Step 5. 啟動Hadoop相關service:
  * 格式化HDFS：
  ```bash
  hdfs namenode -format
  ```
  * 啟動NameNode與DataNode的daemon：    
    * 如果沒有設定免密碼，在此步驟會需要輸入多次的使用者密碼。
    ```bash
    cd $HADOOP_HOME/sbin
    ./start-dfs.sh
    ```
    * 啟動成功後，可透過`http://localhost:50070`連線至Web UI，畫面如下：
    ![hadoop_web_ui.png](../../../../img/30_days/hadoop_web_ui.png)
  * 啟動Yarn：    
    * 如果沒有設定免密碼，在此步驟會需要輸入多次的使用者密碼。
    ```bash
    cd $HADOOP_HOME/sbin
    ./start-yarn.sh
    ```
    * 啟動成功後，可透過`http://localhost:8088`連線至Web UI，畫面如下：
    ![yarn_web_ui.png](../../../../img/30_days/yarn_web_ui.png)
* 如何關閉Hadoop相關service？
  * 關閉NameNode與DataNode
  ```bash
  cd $HADOOP_HOME/sbin
  ./stop-dfs.sh
  ```
  * 關閉Yarn：
    ```bash
    cd $HADOOP_HOME/sbin
    ./stop-yarn.sh
    ```

## 開始安裝 - 多機安裝模式 Fully-Distributed

基本上與偽分佈模式 (Pseudo-Distributed)的安裝方法幾乎一模ㄧ樣，只需要多設定幾個檔案即可。`強烈建議要啟動hadoop service的所有機器一定要設定免密碼ssh`，否則輸入密碼就非常頭大！

下列的步驟需要在每一台hadoop cluster的機器上設定：
* 編輯Hadoop設定檔
  * core-site.xml
  ```bash
  sudo vi $HADOOP_HOME/etc/hadoop/core-site.xml
  ```
  將`{hostname}`修改為主機名稱，指定本機為HDFS的路徑(在偽分佈為：hdfs://localhost:9000)：
  ```bash
  <configuration>
      <property>
          <name>fs.defaultFS</name>
          <value>hdfs://{hostname}:9000</value>
      </property>
  </configuration>
  ```
  * Slaves
  ```bash
  sudo vi $HADOOP_HOME/etc/hadoop/slaves
  ```
  加入將hadoop cluster的機器名稱全部加入。請將server-a1等名稱改為符合自己環境的hostname或是IP。
  ```bash
  server-a1
  server-a2
  server-a3
  ```
* 編輯/etc/hosts    
如果$HADOOP_HOME/etc/hadoop/slaves是以IP設定，那此步驟可以省略。    
由於使用hostname會讓每台電腦無法互相認識，故需要透過設定`/etc/hosts`，將hadoop cluster內所有機器的hostname與ip加入此檔案。

設定完畢後，多機安裝模式 (Fully-Distributed)啟動與停止的指令都與偽分佈模式 (Pseudo-Distributed)相同，此時你已經有一個真正的分散式儲存與運算的平台了！

下一篇要來介紹Hadoop平台上的指令！
