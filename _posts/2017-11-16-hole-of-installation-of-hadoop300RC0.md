---
layout:     post
title:      "安裝Hadoop 3.0.0 RC0時所遇到的坑"
subtitle:   "The holes of Hadoop 3.0.0 RC0 when installing."
date:       2017-11-16 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

由於Hadoop 3.0.0 快接近正式釋出的階段，所以最近投票活動頻繁，參與投票也是社群活動的日常。    
作者是個很懶的人，所以使用了Docker做了一個image的範本，只要把版本下載點替換後，rebuild image就可以幫我把Hadoop cluster建置起來，我可以利用Docker在建置的空擋做其他事情。

就在Hadoop 3.0.0RC0的Docker image建置完畢要啟動叢集時，發生了下列的錯誤訊息：
```bash
hadoop_master | 2017-11-16 02:56:15,560 INFO namenode.NameNode: SHUTDOWN_MSG:
hadoop_master | /************************************************************
hadoop_master | SHUTDOWN_MSG: Shutting down NameNode at hadoop_master/172.19.0.100
hadoop_master | ************************************************************/
hadoop_master | Starting namenodes on [hadoop_master]
hadoop_master | ERROR: Attempting to operate on hdfs namenode as root
hadoop_master | ERROR: but there is no HDFS_NAMENODE_USER defined. Aborting operation.
hadoop_master | Starting datanodes
hadoop_master | ERROR: Attempting to operate on hdfs datanode as root
hadoop_master | ERROR: but there is no HDFS_DATANODE_USER defined. Aborting operation.
hadoop_master | Starting secondary namenodes [hadoop_master]
hadoop_master | ERROR: Attempting to operate on hdfs secondarynamenode as root
hadoop_master | ERROR: but there is no HDFS_SECONDARYNAMENODE_USER defined. Aborting operation.
hadoop_master | 2017-11-16 02:56:19,817 WARN util.NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
hadoop_master | Starting resourcemanager
hadoop_master | ERROR: Attempting to operate on yarn resourcemanager as root
hadoop_master | ERROR: but there is no YARN_RESOURCEMANAGER_USER defined. Aborting operation.
hadoop_master | Starting nodemanagers
hadoop_master | ERROR: Attempting to operate on yarn nodemanager as root
hadoop_master | ERROR: but there is no YARN_NODEMANAGER_USER defined. Aborting operation.
```

相同的安裝設定在hadoop 2.7.x是可以正常啟動的，但是hadoop 3.0.0就不行囉。

**解決方法:**    
編輯${HADOOP_HOME}/etc/hadoop/hadoop-env.sh，加入下列設定:
```bash
export HDFS_DATANODE_USER=root
export HADOOP_SECURE_DN_USER=hdfs
export HDFS_NAMENODE_USER=root
export HDFS_SECONDARYNAMENODE_USER=root
```
**由於叢集為測試使用，故使用者設定為root，正式環境請根據企業內部資安規範設置。**

<h2>後記：</h2>    
使用關鍵字：`HDFS_NAMENODE_USER`請google大神查完之後，找到了[Hadoop 3.0.0-beta1官方文件](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/UnixShellGuide.html#a.28command.29_.28subcommand.29_USER)的說明，看起來像是security相關設定，需要Hadoop神人來幫忙講解了！
