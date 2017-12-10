---
layout:     post
title:      "30天系列 Day 7-MapReduce WordCount流程介紹"
subtitle:   "30 Days Series Day 7 - Walk-through of WordCount."
date:       2017-12-11 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

## MapReduce WordCount 流程

由於這次計算的檔案有兩個，因為檔案容量小於HDFS一個block的大小(預設為64mb)，上傳至HDFS後分別會被儲存至兩個block，執行MapReduce運算時，預設也會啟動兩個mapper來執行運算。
