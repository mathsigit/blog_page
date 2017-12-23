---
layout:     post
title:      "30天系列 Day 18-Apache Spark API"
subtitle:   "30 Days Series Day 18 - Introduction Of Apache Spark API"
date:       2017-12-22 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Spark Hello World API

今天要來介紹如何撰寫一段簡單的Spark Hello World API程式碼。

接下來的範例要以python為例。使用python來進行spark submit的好處是不用打包成jar檔，    
但如果有使用到其他的第三方library，進行spark submit時需要將檔案同時上傳。

首先先打開一個空白文件 (如果有使用IDE會更棒！建議搭配使用 IntelliJ )，並命名成`helloworld.py`。

```python
import sys
from pyspark import SparkContext

def computeStatsForCollection(sc,countPerPartitions=100000,partitions=5):
	totalNumber = min( countPerPartitions * partitions, sys.maxsize)
	rdd = sc.parallelize( range(totalNumber),partitions)
	return (rdd.mean(), rdd.variance())
	
if __name__ == "__main__":
	sc = SparkContext(appName="Hello Spark")
	print("Hello Spark Demo. Compute the mean and variance of a collection")
	stats = computeStatsForCollection(sc);
	print(">>> Results: ")
	print(">>>>>>>Mean: " + str(stats[0]));
	print(">>>>>>>Variance: " + str(stats[1]));
	sc.stop()
```

接下來只要使用spark submit指令就可以在local執行我們的程式囉！

```bash
./spark-submit --master local helloworld.py
```

運作的過程及結果：

```bash
Using Spark's default log4j profile: org/apache/spark/log4j-defaults.properties
17/12/22 18:56:46 INFO SparkContext: Running Spark version 2.1.2
17/12/22 18:56:47 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/12/22 18:56:47 INFO SecurityManager: Changing view acls to: stana
17/12/22 18:56:47 INFO SecurityManager: Changing modify acls to: stana
17/12/22 18:56:47 INFO SecurityManager: Changing view acls groups to:
17/12/22 18:56:47 INFO SecurityManager: Changing modify acls groups to:
17/12/22 18:56:47 INFO SecurityManager: SecurityManager: authentication disabled; ui acls disabled; users  with view permissions: Set(stana); groups with view permissions: Set(); users  with modify permissions: Set(stana); groups with modify permissions: Set()
17/12/22 18:56:48 INFO Utils: Successfully started service 'sparkDriver' on port 63573.
17/12/22 18:56:48 INFO SparkEnv: Registering MapOutputTracker
17/12/22 18:56:48 INFO SparkEnv: Registering BlockManagerMaster
17/12/22 18:56:48 INFO BlockManagerMasterEndpoint: Using org.apache.spark.storage.DefaultTopologyMapper for getting topology information
17/12/22 18:56:48 INFO BlockManagerMasterEndpoint: BlockManagerMasterEndpoint up
17/12/22 18:56:48 INFO DiskBlockManager: Created local directory at /private/var/folders/90/_z_w8bmd0k3gsv4y03clngfw0000gn/T/blockmgr-8e76e493-de86-4167-a3ab-cde2f0d62c6a
17/12/22 18:56:48 INFO MemoryStore: MemoryStore started with capacity 366.3 MB
17/12/22 18:56:48 INFO SparkEnv: Registering OutputCommitCoordinator
17/12/22 18:56:49 INFO Utils: Successfully started service 'SparkUI' on port 4040.
17/12/22 18:56:49 INFO SparkUI: Bound SparkUI to 0.0.0.0, and started at http://10.1.1.123:4040
17/12/22 18:56:49 INFO SparkContext: Added file file:/Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py at file:/Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py with timestamp 1513940209546
17/12/22 18:56:49 INFO Utils: Copying /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py to /private/var/folders/90/_z_w8bmd0k3gsv4y03clngfw0000gn/T/spark-2884e48f-df26-40ef-bc34-2e5cb9f03d1e/userFiles-3859a0da-0a04-4aa8-af98-7fe78b4dcfef/helloworld.py
17/12/22 18:56:49 INFO Executor: Starting executor ID driver on host localhost
17/12/22 18:56:49 INFO Utils: Successfully started service 'org.apache.spark.network.netty.NettyBlockTransferService' on port 63574.
17/12/22 18:56:49 INFO NettyBlockTransferService: Server created on 10.1.1.123:63574
17/12/22 18:56:49 INFO BlockManager: Using org.apache.spark.storage.RandomBlockReplicationPolicy for block replication policy
17/12/22 18:56:49 INFO BlockManagerMaster: Registering BlockManager BlockManagerId(driver, 10.1.1.123, 63574, None)
17/12/22 18:56:49 INFO BlockManagerMasterEndpoint: Registering block manager 10.1.1.123:63574 with 366.3 MB RAM, BlockManagerId(driver, 10.1.1.123, 63574, None)
17/12/22 18:56:49 INFO BlockManagerMaster: Registered BlockManager BlockManagerId(driver, 10.1.1.123, 63574, None)
17/12/22 18:56:49 INFO BlockManager: Initialized BlockManager: BlockManagerId(driver, 10.1.1.123, 63574, None)
Hello Spark Demo. Compute the mean and variance of a collection
17/12/22 18:56:50 INFO SparkContext: Starting job: mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7
17/12/22 18:56:50 INFO DAGScheduler: Got job 0 (mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7) with 5 output partitions
17/12/22 18:56:50 INFO DAGScheduler: Final stage: ResultStage 0 (mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7)
17/12/22 18:56:50 INFO DAGScheduler: Parents of final stage: List()
17/12/22 18:56:50 INFO DAGScheduler: Missing parents: List()
17/12/22 18:56:50 INFO DAGScheduler: Submitting ResultStage 0 (PythonRDD[1] at mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7), which has no missing parents
17/12/22 18:56:51 INFO MemoryStore: Block broadcast_0 stored as values in memory (estimated size 4.5 KB, free 366.3 MB)
17/12/22 18:56:51 INFO MemoryStore: Block broadcast_0_piece0 stored as bytes in memory (estimated size 2.9 KB, free 366.3 MB)
17/12/22 18:56:51 INFO BlockManagerInfo: Added broadcast_0_piece0 in memory on 10.1.1.123:63574 (size: 2.9 KB, free: 366.3 MB)
17/12/22 18:56:51 INFO SparkContext: Created broadcast 0 from broadcast at DAGScheduler.scala:996
17/12/22 18:56:51 INFO DAGScheduler: Submitting 5 missing tasks from ResultStage 0 (PythonRDD[1] at mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7)
17/12/22 18:56:51 INFO TaskSchedulerImpl: Adding task set 0.0 with 5 tasks
17/12/22 18:56:51 WARN TaskSetManager: Stage 0 contains a task of very large size (364 KB). The maximum recommended task size is 100 KB.
17/12/22 18:56:51 INFO TaskSetManager: Starting task 0.0 in stage 0.0 (TID 0, localhost, executor driver, partition 0, PROCESS_LOCAL, 373371 bytes)
17/12/22 18:56:51 INFO Executor: Running task 0.0 in stage 0.0 (TID 0)
17/12/22 18:56:51 INFO Executor: Fetching file:/Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py with timestamp 1513940209546
17/12/22 18:56:51 INFO Utils: /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py has been previously copied to /private/var/folders/90/_z_w8bmd0k3gsv4y03clngfw0000gn/T/spark-2884e48f-df26-40ef-bc34-2e5cb9f03d1e/userFiles-3859a0da-0a04-4aa8-af98-7fe78b4dcfef/helloworld.py
17/12/22 18:56:52 INFO PythonRunner: Times: total = 1335, boot = 398, init = 18, finish = 919
17/12/22 18:56:52 INFO Executor: Finished task 0.0 in stage 0.0 (TID 0). 1880 bytes result sent to driver
17/12/22 18:56:52 INFO TaskSetManager: Starting task 1.0 in stage 0.0 (TID 1, localhost, executor driver, partition 1, PROCESS_LOCAL, 509839 bytes)
17/12/22 18:56:52 INFO Executor: Running task 1.0 in stage 0.0 (TID 1)
17/12/22 18:56:52 INFO TaskSetManager: Finished task 0.0 in stage 0.0 (TID 0) in 1566 ms on localhost (executor driver) (1/5)
17/12/22 18:56:53 INFO PythonRunner: Times: total = 935, boot = -21, init = 22, finish = 934
17/12/22 18:56:53 INFO Executor: Finished task 1.0 in stage 0.0 (TID 1). 1793 bytes result sent to driver
17/12/22 18:56:53 INFO TaskSetManager: Starting task 2.0 in stage 0.0 (TID 2, localhost, executor driver, partition 2, PROCESS_LOCAL, 509839 bytes)
17/12/22 18:56:53 INFO Executor: Running task 2.0 in stage 0.0 (TID 2)
17/12/22 18:56:53 INFO TaskSetManager: Finished task 1.0 in stage 0.0 (TID 1) in 955 ms on localhost (executor driver) (2/5)
17/12/22 18:56:54 INFO PythonRunner: Times: total = 937, boot = -4, init = 5, finish = 936
17/12/22 18:56:54 INFO Executor: Finished task 2.0 in stage 0.0 (TID 2). 1793 bytes result sent to driver
17/12/22 18:56:54 INFO TaskSetManager: Starting task 3.0 in stage 0.0 (TID 3, localhost, executor driver, partition 3, PROCESS_LOCAL, 509839 bytes)
17/12/22 18:56:54 INFO Executor: Running task 3.0 in stage 0.0 (TID 3)
17/12/22 18:56:54 INFO TaskSetManager: Finished task 2.0 in stage 0.0 (TID 2) in 953 ms on localhost (executor driver) (3/5)
17/12/22 18:56:55 INFO PythonRunner: Times: total = 931, boot = -6, init = 7, finish = 930
17/12/22 18:56:55 INFO Executor: Finished task 3.0 in stage 0.0 (TID 3). 1880 bytes result sent to driver
17/12/22 18:56:55 INFO TaskSetManager: Starting task 4.0 in stage 0.0 (TID 4, localhost, executor driver, partition 4, PROCESS_LOCAL, 506157 bytes)
17/12/22 18:56:55 INFO Executor: Running task 4.0 in stage 0.0 (TID 4)
17/12/22 18:56:55 INFO TaskSetManager: Finished task 3.0 in stage 0.0 (TID 3) in 947 ms on localhost (executor driver) (4/5)
17/12/22 18:56:56 INFO PythonRunner: Times: total = 921, boot = -2, init = 3, finish = 920
17/12/22 18:56:56 INFO Executor: Finished task 4.0 in stage 0.0 (TID 4). 1793 bytes result sent to driver
17/12/22 18:56:56 INFO TaskSetManager: Finished task 4.0 in stage 0.0 (TID 4) in 935 ms on localhost (executor driver) (5/5)
17/12/22 18:56:56 INFO TaskSchedulerImpl: Removed TaskSet 0.0, whose tasks have all completed, from pool
17/12/22 18:56:56 INFO DAGScheduler: ResultStage 0 (mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7) finished in 5.362 s
17/12/22 18:56:56 INFO DAGScheduler: Job 0 finished: mean at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7, took 5.809938 s
17/12/22 18:56:56 INFO SparkContext: Starting job: variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7
17/12/22 18:56:56 INFO DAGScheduler: Got job 1 (variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7) with 5 output partitions
17/12/22 18:56:56 INFO DAGScheduler: Final stage: ResultStage 1 (variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7)
17/12/22 18:56:56 INFO DAGScheduler: Parents of final stage: List()
17/12/22 18:56:56 INFO DAGScheduler: Missing parents: List()
17/12/22 18:56:56 INFO DAGScheduler: Submitting ResultStage 1 (PythonRDD[2] at variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7), which has no missing parents
17/12/22 18:56:56 INFO MemoryStore: Block broadcast_1 stored as values in memory (estimated size 4.5 KB, free 366.3 MB)
17/12/22 18:56:56 INFO MemoryStore: Block broadcast_1_piece0 stored as bytes in memory (estimated size 2.9 KB, free 366.3 MB)
17/12/22 18:56:56 INFO BlockManagerInfo: Added broadcast_1_piece0 in memory on 10.1.1.123:63574 (size: 2.9 KB, free: 366.3 MB)
17/12/22 18:56:56 INFO SparkContext: Created broadcast 1 from broadcast at DAGScheduler.scala:996
17/12/22 18:56:56 INFO DAGScheduler: Submitting 5 missing tasks from ResultStage 1 (PythonRDD[2] at variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7)
17/12/22 18:56:56 INFO TaskSchedulerImpl: Adding task set 1.0 with 5 tasks
17/12/22 18:56:56 WARN TaskSetManager: Stage 1 contains a task of very large size (364 KB). The maximum recommended task size is 100 KB.
17/12/22 18:56:56 INFO TaskSetManager: Starting task 0.0 in stage 1.0 (TID 5, localhost, executor driver, partition 0, PROCESS_LOCAL, 373375 bytes)
17/12/22 18:56:56 INFO Executor: Running task 0.0 in stage 1.0 (TID 5)
17/12/22 18:56:57 INFO PythonRunner: Times: total = 906, boot = -74, init = 75, finish = 905
17/12/22 18:56:57 INFO Executor: Finished task 0.0 in stage 1.0 (TID 5). 1866 bytes result sent to driver
17/12/22 18:56:57 INFO TaskSetManager: Starting task 1.0 in stage 1.0 (TID 6, localhost, executor driver, partition 1, PROCESS_LOCAL, 509843 bytes)
17/12/22 18:56:57 INFO Executor: Running task 1.0 in stage 1.0 (TID 6)
17/12/22 18:56:57 INFO TaskSetManager: Finished task 0.0 in stage 1.0 (TID 5) in 925 ms on localhost (executor driver) (1/5)
17/12/22 18:56:58 INFO PythonRunner: Times: total = 927, boot = -1, init = 2, finish = 926
17/12/22 18:56:58 INFO Executor: Finished task 1.0 in stage 1.0 (TID 6). 1880 bytes result sent to driver
17/12/22 18:56:58 INFO TaskSetManager: Starting task 2.0 in stage 1.0 (TID 7, localhost, executor driver, partition 2, PROCESS_LOCAL, 509843 bytes)
17/12/22 18:56:58 INFO Executor: Running task 2.0 in stage 1.0 (TID 7)
17/12/22 18:56:58 INFO TaskSetManager: Finished task 1.0 in stage 1.0 (TID 6) in 937 ms on localhost (executor driver) (2/5)
17/12/22 18:56:59 INFO PythonRunner: Times: total = 927, boot = -1, init = 2, finish = 926
17/12/22 18:56:59 INFO Executor: Finished task 2.0 in stage 1.0 (TID 7). 1793 bytes result sent to driver
17/12/22 18:56:59 INFO TaskSetManager: Starting task 3.0 in stage 1.0 (TID 8, localhost, executor driver, partition 3, PROCESS_LOCAL, 509843 bytes)
17/12/22 18:56:59 INFO Executor: Running task 3.0 in stage 1.0 (TID 8)
17/12/22 18:56:59 INFO TaskSetManager: Finished task 2.0 in stage 1.0 (TID 7) in 936 ms on localhost (executor driver) (3/5)
17/12/22 18:57:00 INFO PythonRunner: Times: total = 929, boot = 1, init = 0, finish = 928
17/12/22 18:57:00 INFO Executor: Finished task 3.0 in stage 1.0 (TID 8). 1793 bytes result sent to driver
17/12/22 18:57:00 INFO TaskSetManager: Starting task 4.0 in stage 1.0 (TID 9, localhost, executor driver, partition 4, PROCESS_LOCAL, 506161 bytes)
17/12/22 18:57:00 INFO Executor: Running task 4.0 in stage 1.0 (TID 9)
17/12/22 18:57:00 INFO TaskSetManager: Finished task 3.0 in stage 1.0 (TID 8) in 938 ms on localhost (executor driver) (4/5)
17/12/22 18:57:01 INFO PythonRunner: Times: total = 926, boot = 0, init = 0, finish = 926
17/12/22 18:57:01 INFO Executor: Finished task 4.0 in stage 1.0 (TID 9). 1793 bytes result sent to driver
17/12/22 18:57:01 INFO TaskSetManager: Finished task 4.0 in stage 1.0 (TID 9) in 933 ms on localhost (executor driver) (5/5)
17/12/22 18:57:01 INFO TaskSchedulerImpl: Removed TaskSet 1.0, whose tasks have all completed, from pool
17/12/22 18:57:01 INFO DAGScheduler: ResultStage 1 (variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7) finished in 4.664 s
17/12/22 18:57:01 INFO DAGScheduler: Job 1 finished: variance at /Volumes/Sdhd/Downloads/spark-2.1.2-bin-hadoop2.7/bin/helloworld.py:7, took 4.677022 s
>>> Results:
>>>>>>>Mean: 249999.5
>>>>>>>Variance: 20833333333.2
17/12/22 18:57:01 INFO SparkUI: Stopped Spark web UI at http://10.1.1.123:4040
17/12/22 18:57:01 INFO MapOutputTrackerMasterEndpoint: MapOutputTrackerMasterEndpoint stopped!
17/12/22 18:57:01 INFO MemoryStore: MemoryStore cleared
17/12/22 18:57:01 INFO BlockManager: BlockManager stopped
17/12/22 18:57:01 INFO BlockManagerMaster: BlockManagerMaster stopped
17/12/22 18:57:01 INFO OutputCommitCoordinator$OutputCommitCoordinatorEndpoint: OutputCommitCoordinator stopped!
17/12/22 18:57:01 INFO SparkContext: Successfully stopped SparkContext
17/12/22 18:57:02 INFO ShutdownHookManager: Shutdown hook called
17/12/22 18:57:02 INFO ShutdownHookManager: Deleting directory /private/var/folders/90/_z_w8bmd0k3gsv4y03clngfw0000gn/T/spark-2884e48f-df26-40ef-bc34-2e5cb9f03d1e/pyspark-7a2eb92b-ea5a-47b4-9595-f5bc069fe579
17/12/22 18:57:02 INFO ShutdownHookManager: Deleting directory /private/var/folders/90/_z_w8bmd0k3gsv4y03clngfw0000gn/T/spark-2884e48f-df26-40ef-bc34-2e5cb9f03d1e
```

大家可以看到這段文字：

```bash
>>> Results:
>>>>>>>Mean: 249999.5
>>>>>>>Variance: 20833333333.2
```
就是我們在程式裡面印出來的文字。

# 最後

這段程式碼是使用 spark submit 來進行運作，如果對spark submit不了解沒關係，接下來我們就要來介紹它了！