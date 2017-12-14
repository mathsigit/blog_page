---
layout:     post
title:      "30天系列 Day 10-HBase Shell"
subtitle:   "30 Days Series Day 10 - HBase Shell."
date:       2017-12-14 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

# HBase2 Shell Usage

HBase Shell是使用 [(J)Ruby's][jruby_official] IRB(Interactive Ruby Shell)撰寫而成的特殊指令，使用者可以透過它下指令來與HBase互動。

## HBase Shell 入口點

假設已經完成前一篇的HBase安裝，接著只要使用下面指令就可以使用HBase Shell與HBase互動。
```bash
hbase shell
```
是的！你沒看過，HBase Shell的進入指令就是`hbase shell`。接下來會透過一個使用情境來介紹基本的HBase shell指令。

## HBase Shell 使用情境

一開始使用一個新的DataBase時，會需要先新增Table。成功建立好Table後，會需要新增資料。爾後會有編輯舊資料的需求。最後，查詢資料。

### 建立資料表

```bash
#Usage
create 'table_name', 'column_family_name'
#說明
  table_name:    欲建立的table名稱。
  column_family: Column Family名稱。Column Family是用來將欄位分群，被分群的欄位名稱會以Column Family
                 為前綴詞(prefix)，冒號(:)為分隔符號，再加上qualifier(column name)的組合呈現。
                 例如：在某個table內會有欄位`tag:name`與`tag:id`，其中`tag`代表column family，
                 `name`與`id`為qualifier。
```

接下來我們就來建立一個`firstTable`的資料表，column family名稱為`first_cf`：
```bash
create 'firstTable', 'first_cf'
```

### 查詢資料表清單

資料表建立成功後，可用下列指令來查看資料表清單：
```bash
list
```
就可以看到剛剛建立好的`firstTable`：
```bash
TABLE
firstTable
1 row(s)
Took 0.5339 seconds
```

### 新增資料

接下來要新增資料：
```bash
#Usage
put 'table_name', 'row_key', 'column_name', 'value'
#說明
  table_name:     table名稱。
  row_key:        row_key，即HBase的Index，也是唯一的主鍵(pk)。
  column_name:    欄位名稱，請以column_family:qualifier格式輸入。
  value:          欄位值。
```
接下來可以使用下面語法來新增筆資料：
```bash
put 'firstTable', 'rk_1', 'first_cf:value', '65535'
put 'firstTable', 'rk_2', 'first_cf:value', '111'
put 'firstTable', 'rk_3', 'first_cf:value', '9487'
put 'firstTable', 'rk_3', 'first_cf:id', 'John'
```

### 查詢資料

新增一筆資料後，接著就要查詢剛剛新增的資料是否正確：
```bash
#Usage
scan 'table_name'
#說明
  table_name:     table名稱。
```
接下來可以使用下面語法來查詢table資料：
```bash
scan 'firstTable'

#結果
ROW                   COLUMN+CELL
 rk_1                 column=first_cf:value, timestamp=1513237353219, value=65535
 rk_2                 column=first_cf:value, timestamp=1513238888458, value=111
 rk_3                 column=first_cf:id, timestamp=1513238938372, value=John
 rk_3                 column=first_cf:value, timestamp=1513238904507, value=9487
3 row(s)
Took 0.0189 seconds
```
由於前面有提到HBase是以key-value方式儲存資料，所以在這裡可以看到呈現方式也是以key-value方式呈現。    
想像成table方式會變成這樣：    

rowkey\column|id|value
----|--------|--------
rk_1|    |65535
rk_2|    |111
rk_3|John|9487
這就是前面所提到的稀疏矩陣（Sparse matrix），每個row有資料的欄位數並不一定會相等。使用scan會將該table內所有資料全部以key-value方式顯示在銀幕，使用時務必搭配`LIMIT`參數控制顯示的資料數量。
```bash
#只回傳一筆資料
scan 'firstTable', {LIMIT => 1}
```

### 更新資料

HBase沒有update的指令，要更新資料只能再對同一個欄位進行一次put：
```bash
put 'firstTable', 'rk_1', 'first_cf:value', '123456'
```
使用filter取出剛剛put的資料:
```bash
scan 'firstTable', {ROWPREFIXFILTER => 'rk_1', COLUMNS => ['first_cf:value']}

#結果
ROW                   COLUMN+CELL
 rk_1                 column=first_cf:value, timestamp=1513241216522, value=123456
1 row(s)
Took 0.0365 seconds
```

## 最後

看完了HBase Shell的入門指令後，接下來要來介紹HBase Rowkey Design。

[jruby_official]: http://jruby.org/
