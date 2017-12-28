---
layout:     post
title:      "在Macos上開發dotnet程式"
subtitle:   "Developing dotnet project on macos"
date:       2017-12-28 00:00:00 +0800
author:     "Yung-An"
header-img: "img/technical-post-bg.png"
---

紀錄如何在macOS開發dotnet project。

首先需要安裝:
* vs code
  * 到微軟官方網站下載
* dotnet sdk
  * 可透過`homebrew`使用 `brew install caskroom/cask/dotnet-sdk` 指令安裝
* 從 Visual Studio Code Marketplace 安裝 C# extensions

建立專案資料夾後，以vs code開啟專案資料夾，並開啟**Integrated Terminal**

## 初始化專案

輸入指令`dotnet new console`，以便初始化專案。

## 執行

輸入指令`dotnet run`