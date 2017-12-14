---
layout:     post
title:      "30天系列 Day 10-HBase Shell"
subtitle:   "30 Days Series Day 10 - HBase Shell."
date:       2017-12-14 00:00:00
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

HBase Shell是使用 [(J)Ruby's][jruby_official] IRB(Interactive Ruby Shell)撰寫而成的特殊指令，使用者可以透過它下指令來與HBase互動。

## HBase Shell 入口點

假設已經完成前一篇的HBase安裝，接著只要使用下面指令就可以使用HBase Shell與HBase互動。
```bash
hbase shell
```
是的！你沒看過，HBase Shell的進入指令就是`hbase shell`。

[jruby_official]: http://jruby.org/
