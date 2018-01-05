---
layout:     post
title:      "30天系列 Day 29-End Of 30 Days Series"
subtitle:   "30 Days Series Day 29 - End Of 30 Days Series"
date:       2018-01-02 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

30 天系列到了尾聲，最後來談談Hadoop Ecosystem的未來。 
Hadoop剛發表的時候影響整個檔案系統的生態，以往在單一機器無法處理或是儲存有所限制的使用情境都因Hadoop得以解決。

但由於MapReduce的應用程式對於開發者而言較不友善，且以運算速度的角度來看，對於已經習慣**毫秒等級**內反應的使用者來說，MapReduce還是有很大的改善空間。所以當Spark發表後，在短時間內就受到高度注目，使用人數也急速攀升。

很多人的問題是，"Hadoop 會不會被 Spark所取代呢？"，這要看以哪個角度來看這件事情。Spark是個運用記憶體計算的運算框架(Framework)而MapReduce在運算過程中會不斷地將各Map的運算結果儲存在硬碟內，硬碟IO相較於使用記憶體會花費較多的時間，所以**Spark在運算方面基本上是勝過MapReduce**。Saprk唯一無法勝過Hadoop的大概就是儲存系統了吧！因為Saprk是個運算框架而不具備儲存功能。

Hadoop社群這幾年也朝向這方面努力，先後整合[Kerberos][kerberos_url]與[Sentry][sentry_url]，彌補了authentication與authorization權限的不足，除了讓使用者資料受到保護以外，也可以達成Multi-tenancy的功能。

倘若Hadoop未來可以讓HDFS的功能更加強大、安全與穩定，其周邊的Ecosystem也會更加欣欣向榮，尤其是首當其衝的HBase。

如果你是個熱血開發者，想讓整個Hadoop Ecosystem更加好用，又或者覺得"這ＸＸＸ功能怎麼這麼難用，如果是OOOO這樣做會更好啊"，社群們需要你的加入！！

可以到各個Mailing list:

* [Hadoop][hadoop_mail]
* [HBase][hbase_mail]
* [Hive][hive_mail]
* [Spark][spark_mail]

或是[Jira][jira]參與並討論各個project的開發與討論，讓社群了解使用者的心聲，或遮直接參與開發討論是更好的！

[jira]: https://issues.apache.org/jira/browse/PHOENIX-4506?jql=project%20in%20(SPARK%2C%20HBASE%2C%20HADOOP%2C%20HIVE)
[kerberos_url]: https://web.mit.edu/kerberos/
[sentry_url]: https://sentry.apache.org
[hadoop_mail]: https://hadoop.apache.org/general_lists.html
[hbase_mail]: https://hbase.apache.org/mail-lists.html
[hive_mail]: https://hive.apache.org/mailing_lists.html
[spark_mail]: https://spark.apache.org/community.html