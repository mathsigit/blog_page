---
layout:     post
title:      "Hadoop 3.0.0 RC0執行MR Job時所遇到的坑"
subtitle:   "The holes of Hadoop 3.0.0 RC0 when submitting MR Job."
date:       2017-11-16 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

繼解決[安裝 Hadoop 3.0.0RC0 時所遇到的
坑][hole-of-installation-of-hadoop300rc0]後，問題就這樣沒了嗎？代治不是憨人想得
這麼簡單！就在執行 `hadoop jar
$HADOOP_HOME/share/hadoop/mapreduce/hadoop-mapreduce-examples-3.0.0.jar pi 16
1000`後，出現了在 2.x 版本沒出現過的錯誤訊息：

```bash
Application application_1511776681054_0001 failed 2 times due to AM Container for appattempt_1511776681054_0001_000002 exited with exitCode: 1
Failing this attempt.Diagnostics: [2017-11-27 10:01:30.473]Exception from container-launch.
Container id: container_1511776681054_0001_02_000001
Exit code: 1
[2017-11-27 10:01:30.473]
[2017-11-27 10:01:30.529]Container exited with a non-zero exit code 1. Error file: prelaunch.err.
Last 4096 bytes of prelaunch.err :
Last 4096 bytes of stderr :
Error: Could not find or load main class org.apache.hadoop.mapreduce.v2.app.MRAppMaster
Please check whether your etc/hadoop/mapred-site.xml contains the below configuration:
<property>
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=${full path of your hadoop distribution directory}</value>
</property>
<property>
<name>mapreduce.map.env</name>
<value>HADOOP_MAPRED_HOME=${full path of your hadoop distribution directory}</value>
</property>
<property>
<name>mapreduce.reduce.env</name>
<value>HADOOP_MAPRED_HOME=${full path of your hadoop distribution directory}</value>
</property>
[2017-11-27 10:01:30.529]
[2017-11-27 10:01:30.530]Container exited with a non-zero exit code 1. Error file: prelaunch.err.
Last 4096 bytes of prelaunch.err :
Last 4096 bytes of stderr :
Error: Could not find or load main class org.apache.hadoop.mapreduce.v2.app.MRAppMaster
Please check whether your etc/hadoop/mapred-site.xml contains the below configuration:
<property>
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=${full path of your hadoop distribution directory}</value>
</property>
<property>
<name>mapreduce.map.env</name>
<value>HADOOP_MAPRED_HOME=${full path of your hadoop distribution directory}</value>
</property>
<property>
<name>mapreduce.reduce.env</name>
<value>HADOOP_MAPRED_HOME=${full path of your hadoop distribution directory}</value>
</property>
[2017-11-27 10:01:30.530]
For more detailed output, check the application tracking page: http://hadoop-master:8088/cluster/app/application_1511776681054_0001 Then click on links to logs of each attempt.
. Failing the application.
```

後來在 Hadoop JIRA 發現了一個 issue [MAPREDUCE-6702][mapreduce-6702]，引用部分說明：

> In Hadoop 3, YARN containers do not inherit the NodeManagers' environment
> variables. Therefore, if you want to inherit NodeManager's environment
> variables (e.g. `HADOOP_MAPRED_HOME`), you need to set additional parameters
> (e.g. `mapreduce.admin.user.env` and `yarn.app.mapreduce.am.env`).

表示hadoop 3.0.0版本之後，各個service的環境變數已經不互相繼承，必須要經過configuration設定才行。那如何解決這個錯誤訊息？根據錯誤訊息的提示，在`$HADOOP_HOME/etc/hadoop/mapred-site.xml`內加入下列設定即可：
```bash
<property>
<name>yarn.app.mapreduce.am.env</name>
<value>HADOOP_MAPRED_HOME=/opt/hadoop</value>
</property>
<property>
<name>mapreduce.map.env</name>
<value>HADOOP_MAPRED_HOME=/opt/hadoop</value>
</property>
<property>
<name>mapreduce.reduce.env</name>
<value>HADOOP_MAPRED_HOME=/opt/hadoop</value>
</property>
```

簡單來說`HADOOP_MAPRED_HOME`=`HADOOP_HOME`，但必須額外設定`HADOOP_MAPRED_HOME`。

[hole-of-installation-of-hadoop300rc0]: /blog_page/2017/11/16/hole-of-installation-of-hadoop300RC0/
[mapreduce-6702]: https://issues.apache.org/jira/browse/MAPREDUCE-6702
