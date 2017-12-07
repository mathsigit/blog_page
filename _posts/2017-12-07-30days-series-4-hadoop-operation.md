---
layout:     post
title:      "30天系列 Day 4-Hadoop操作教學-HDFS篇"
subtitle:   "30 Days Series Day 4 - Operation of HDFS."
date:       2017-12-07 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Day 4 - Hadoop Ecosystem 之 Hadoop HDFS操作教學

在安裝完了Hadoop，並且正常啟動service後，接著我們要來進行HDFS使用教學。與一般的檔案系統一樣，HDFS可以`新增`、`複製`、`刪除`、與`查看`檔案，使用的指令也與Linux非常類似，接下來將會對這些指令進行介紹。

Hadoop平台剛建置好的時候，HDFS除了系統log或是暫存檔案外，是沒有任何使用者的資料。接下來會經過一連串的操作來介紹HDFS指令。

## 使用情境

### 複製檔案至HDFS

首先，先產生一個內文寫著`Hello Wrold`的檔案`test.txt`存儲在本機內：
```bash
echo "Hello Wrold" >> test.txt
```
接著將`test.txt`存放到HDFS根目錄`/`：
```bash
hadoop fs -put test.txt /
```
也可以使用`copyFromLocal`儲存，差別在於檔案來源只限定在local host：
```bash
hadoop fs -copyFromLocal test.txt /
```
---

### 查詢檔案清單

透過下列指令確認`test.txt`是成功儲存到HDFS上：
```bash
hadoop fs -ls /

#回傳結果
-rw-r--r--   3 root supergroup         12 2017-12-07 07:38 /test.txt
drwxr-xr-x   - root supergroup          0 2017-12-6 07:57 /tmp
drwxr-xr-x   - root supergroup          0 2017-12-6 07:57 /user
```
---

### 顯示檔案內容

查詢HDFS上的`test.txt`內容是否為**Hello Wrold**:
```bash
hadoop fs -cat /test.txt

#回傳結果
Hello Wrold
```
---

### 建立資料夾

在HDFS根目錄建立`testFolder`資料夾：
```bash
hadoop fs -mkdir /testFolder

#查詢結果指令
hadoop fs -ls /
#結果
-rw-r--r--   3 root supergroup         12 2017-12-07 07:38 /test.txt
drwxr-xr-x   - root supergroup          0 2017-12-07 09:10 /testFolder
drwxr-xr-x   - root supergroup          0 2017-12-6 07:57  /tmp
drwxr-xr-x   - root supergroup          0 2017-12-6 07:57  /user
```
---

### 移動檔案/資料夾與重新命名

將test.txt檔案由根目錄移動至testFolder內：
```bash
hadoop fs -mv /test.txt /testFolder

#查詢指令
hadoop fs -ls /testFolder
#結果
-rw-r--r--   3 root supergroup         12 2017-12-07 07:38 /testFolder/test.txt
```
重新命名檔案/資料夾一樣是使用`mv`
```bash
hadoop fs -mv /testFolder/test.txt /testFolder/test_rename.txt

#查詢指令
hadoop fs -ls /testFolder
#結果
-rw-r--r--   3 root supergroup         12 2017-12-07 07:38 /testFolder/test_rename.txt
```

---

### 複製檔案/資料夾

在HDFS上複製`/testFolder/test_rename.txt`至根目錄`/`
```bash
hadoop fs -cp /testFolder/test_rename.txt /

#查詢指令
hadoop fs -ls /
#結果
drwxr-xr-x   - root supergroup          0 2017-12-07 09:51 /testFolder
-rw-r--r--   3 root supergroup         12 2017-12-07 10:16 /test_rename.txt
drwxr-xr-x   - root supergroup          0 2017-12-6  07:57 /tmp
drwxr-xr-x   - root supergroup          0 2017-12-6  07:57 /user
```

---

### 複製檔案/資料夾回本機電腦

將`test_rename.txt`複製回本機電腦路徑`/tmp`：
```bash
hadoop fs -get /testFolder/test_rename.txt /tmp

#確認指令
ls -al /tmp
#結果
-rw-r--r--. 1 root root 12 Dec  7 09:58 test_rename.txt
```
也可以使用`copyToLocal`儲存，差別在於複製目的地只限定在local host：
```bash
hadoop fs -copyToLocal /testFolder/test_rename.txt /tmp
```

---

### 刪除HDFS上的檔案/資料夾

刪除`/testFolder`資料夾：
```bash
hadoop fs -rmr /testFolder

#確認指令
hadoop fs -ls /
#結果
-rw-r--r--   3 root supergroup         12 2017-12-07 10:16 /test_rename.txt
drwxr-xr-x   - root supergroup          0 2017-12-6  07:57 /tmp
drwxr-xr-x   - root supergroup          0 2017-12-6  07:57 /user
```

刪除`/test_rename.txt`檔案：

```bash
hadoop fs -rm /test_rename.txt

#確認指令
hadoop fs -ls /
#結果
drwxr-xr-x   - root supergroup          0 2017-12-6  07:57 /tmp
drwxr-xr-x   - root supergroup          0 2017-12-6  07:57 /user
```

使用`rm`或`rmr`指令移除檔案或是資料夾時，會先將檔案搬移到垃圾桶資料夾(trash directory)內，並紀錄該檔案的刪除時間，等經過某段時間後才會真正從HDFS上移除檔案。如果想手動清空垃圾桶，可使用下列指令：
```bash
hadoop fs -expunge
```

若想直接刪除，而不經過垃圾桶的保存機制，可以再刪除時增加`skipTrash`參數：
```bash
hadoop fs -rm -skipTrash /test_rename.txt
```

# 最後

經過一連串的介紹，相信大家一定對HDFS的操作有基本的了解，如果想看這些指令的完整介紹，可以等待下一篇文章！
