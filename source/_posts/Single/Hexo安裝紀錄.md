---
title: Hexo安裝紀錄
date: 2018-04-02 17:34:13
tags: Hexo
categories: 紀錄
---
[Hexo](https://hexo.io/zh-tw/docs/) 、[NexT](https://theme-next.iissnan.com/)


{% note danger %}
- 站內連結若用相對連結在首頁跟文章內顯示的連結路徑不同
- UTF-8編碼中若參雜其他編碼會讓search.xml錯誤而無法搜索
- md檔案若有尖括號未置入code tag 可能造成解析錯誤
{% endnote %}

目標
======
本地端透過Hexo建立網站骨架 並將markdown文章轉為html 上傳到github做為blog網站
<!-- more -->

#### 安裝node.js
> node.js為一本地端的js執行平台 用途廣泛

#### npm (安裝node.js時會一並安裝)
> 一個套件管理系統 能透過命令列安裝套件

#### 安裝git
> 版本控管工具 做為本地端工具與github連結

#### github
> 一個線上的版本控管工具 其附加功能使其能成為http伺服器
> 其Repo需命名為"ACCOUNT_NAME.github.io"


```sh
#安裝hexo命令列工具
npm install -g hexo-cli

#在該目錄建立blog目錄
hexo init blog

#安裝node.js環境
cd blog
npm install

#發佈到github的工具
npm install hexo-deployer-git --save

#使網頁能即時自動更新的工具(會造成一些問題 1.網頁空白 2.搜尋失效)(查問題時最好移除)
npm install hexo-browsersync --save

#下載慣用的blog主題佈局(NexT)
git clone https://github.com/iissnan/hexo-theme-next themes/next

#站內搜尋工具 (:category開頭會無法連結)
npm install hexo-generator-searchdb --save
```


常規操作
======

```sh
#新增文章
hexo new "My New Post"

#啟動本地端server
hexo server

#產生文件
hexo g --watch

#上傳到github
hexo d
```

語法測試
======

# 1
## 2
### 3
#### 4
##### 5
- 1
- 2

{% note info %}測試{% endnote %}

<span class="inline-green">測試</span> <span class="inline-path">測試</span>


 <img src="http://macdown.uranusjr.com/static/images/logo-160.png" width = "50" height = "50" alt="" style="margin-left:0px" />

{% tabs test %}
<!-- tab A -->
  aaa
<!-- endtab -->

<!-- tab B -->
  bbb
<!-- endtab -->
{% endtabs %}
