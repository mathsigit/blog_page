---
layout:     post
title:      "30天系列 Day 4-Hadoop指令介紹-HDFS篇"
subtitle:   "30 Days Series Day 4 - Usage of HDFS."
date:       2017-12-07 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Hadoop HDFS Usage：
```bash
#儲存檔案至HDFS
hadoop fs -put [-f] [-p] [-l] [-d] <localsrc> ... <dst>
參數說明：
  -p : 保留檔案的瀏覽、修改時間、作者和權限。
  -f : 如果儲存的路徑已經存在相同檔名，就複寫該檔案。
  -l : 強制將檔案的儲存副本(replication)變成1。表示該檔案在HDFS上只有一份replication，非必要請勿使
       用，會失去HDFS的可靠(reliable)特點。
  -d : 不建立副檔名為._COPYING_的暫時(temporary)文件。儲存檔案至HDFS過程中會先建立一個副檔名
       為._COPYING_的暫存檔，當儲存完畢後再刪除，若使用這參數將會跳過這步驟。


#查看HDFS某路徑的檔案清單
hadoop fs -ls [-C] [-d] [-h] [-q] [-R] [-t] [-S] [-r] [-u] [<path> ...]
參數說明：
  -C: Display the paths of files and directories only.
  -d: Directories are listed as plain files.
  -h: Format file sizes in a human-readable fashion (eg 64.0m instead of 67108864).
  -q: Print ? instead of non-printable characters.
  -R: Recursively list subdirectories encountered.
  -t: Sort output by modification time (most recent first).
  -S: Sort output by file size.
  -r: Reverse the sort order.
  -u: Use access time rather than modification time for display and sorting.

```
