---
layout:     post
title:      "30天系列 Day 5-Hadoop指令介紹-HDFS篇"
subtitle:   "30 Days Series Day 5 - Usage of HDFS."
date:       2017-12-07 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# Hadoop HDFS Usage

本篇將介紹昨天所提到HDFS相關指令的參數。如果想知道更多的說明，可以使用`hadoop fs -help`指令查看更多。`[]`內的參數可選擇性使用，並非一定要加入指令內。

---

### 複製檔案至HDFS

* `hadoop fs -put [-f] [-p] [-l] [-d] <localsrc> ... <dst>`    
此指令可以同時從本地文件系統複製多個檔案/資料夾到HDFS。

* `hadoop fs -copyFromLocal [-f] [-p] [-l] [-d] <localsrc> URI`
```bash
參數說明：
  -p : 保留檔案的瀏覽、修改時間、擁有者和權限。
  -f : 如果儲存的路徑已經存在相同檔名，就複寫該檔案。
  -l : 強制將檔案的儲存副本(replication)變成1。表示該檔案在HDFS上只有一份replication，非必要請勿使
       用，會失去HDFS的可靠(reliable)特點。
  -d : 不建立副檔名為._COPYING_的暫時(temporary)文件。儲存檔案至HDFS過程中會先建立一個副檔名
       為._COPYING_的暫存檔，當儲存完畢後再刪除，若使用這參數將會跳過這步驟。
```

---

### 查詢檔案清單

* `hadoop fs -ls [-C] [-d] [-h] [-R] [-t] [-S] [-r] [-u] [<path> ...]`
```bash
參數說明：
  -C: 只顯示資料夾與檔案的路徑，略過其它資訊。
  -d: 目錄被列為純文件，只顯示該路徑的資訊。
  -h: 將檔案大小加上單位，便於閱讀（例如，2310會顯示2.3k）。
  -R: 列出路徑內所有子目錄及檔案。
  -t: 按照編輯時間排序（最近的排第一個）。
  -S: 按檔案大小排序。
  -r: 顛倒排列順序。（例如，搭配-t使用就會將距離最遠的時間排第一個）
  -u: 使用訪問時間顯示和排序（預設是使用編輯時間）。
```

---

### 顯示檔案內容

* `hadoop fs -cat [-ignoreCrc] URI [URI ...]`
```bash
參數說明：
  -ignoreCrc: 略過CRC驗證。
```

---

### 建立資料夾

* `hadoop fs -mkdir [-p] <paths>`
```bash
參數說明：
  -p: 類似Unix上 mkdir -p的用法，會將<paths>所有的路徑資料夾全部新增出來。
```

---

### 移動檔案/資料夾與重新命名

* `hadoop fs -mv URI [URI ...] <dest>`    
此指令可以搬移多個檔案/資料夾，而<dest>必須是一個資料夾。此指令無法跨檔案系統搬移（例如：只能HDFS搬移到HDFS，無法由HDFS搬移到local host）

---

### 複製檔案/資料夾

* `hadoop fs -cp [-f] [-p | -p[topax]] URI [URI ...] <dest>`
```bash
參數說明：
  -f: 如果<dest>已經存在，則強制複寫該檔案/資料夾。
  -p: 保留文件屬性(擁有者、修改時間、權限等)。若使用-p而不帶參數，則會保留時間、擁有者與權限。如果使用-pa則保留權限。
```

---

### 複製檔案/資料夾回本機電腦

* `hadoop fs -get [-ignorecrc] [-crc] [-p] [-f] <src> <localdst>`
* `hadoop fs -copyToLocal [-ignorecrc] [-crc] URI <localdst>`
```bash
參數說明：
  -p: 保留檔案的瀏覽和修改時間，擁有者和權限。 （假設權限可以跨越檔案系統複製）
  -f: 如果儲存的路徑已經存在相同檔名，就複寫該檔案。
  -ignorecrc: 略過CRC驗證。
  -crc: 在檔案下載時寫入CRC認證。
```

---

### 刪除HDFS上的檔案/資料夾

* hadoop fs -rmr [-skipTrash] URI [URI ...]    
刪除路徑中所有的檔案與資料夾，可同時刪除多個檔案或資料夾。
```bash
參數說明：
  -skipTrash: 不把檔案入放入垃圾桶而直接刪除。當檔案系統的容量快不足時，這功能將會非常實用。
備註：rmr這個指令已經被標示為deprecated，表示在之後的版本可能會完全
被移除。可以考慮使用hadoop fs -rm -r來代替這個指令。
```
