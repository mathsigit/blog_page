---
layout:     post
title:      "Docker 系列 - Docker Network on VirtualBox"
subtitle:   "Docekr Series - Docker Network on VirtualBox"
date:       2017-12-27 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

前陣子由於某些因素，需要以Vagrant搭建立VM後，在VM內使用Docker建立HBase叢集，而且各HBase work node 需要有 public ip。

剛好Docker 1.19後有支援 macvlan ，看了官方文件就開始使用`docker-compose`啟動三個container。使用compose file 設定 macvlan 網卡與public ip後，各個container可以互通，但無法讓外面的機器連線，後來直接在實體機器上啟動container就可以用public ip連線到container。

## 結論

如果需要讓docker container 擁有 public ip，可以使用macvlan建立網路卡並且指定IP address，但切記只能在實體機器上使用！