---
title: IDE_ENV
date: 2018-04-04 19:04:53
tags: Android
---

## API版本
<img src="https://i.imgur.com/m6oJ5Lu.jpg" width = "50" alt="" />

## SDK TOOL路徑
cd ~/Library/Android/sdk/tools/bin/

```sh
export JAVA_HOME=`/usr/libexec/java_home -v 1.8`

export SDK_HOME=/Users/jimliu/Library/Android/sdk
export ANDROID_HOME=/Users/jimliu/Library/Android/sdk
export GRADLE_HOME=/Applications/Android\ Studio.app/Contents/gradle/gradle-3.2/

export PATH=$JAVA_HOME/bin:${SDK_HOME}/tools/bin:${SDK_HOME}/platform-tools:$GRADLE_HOME/bin:$PATH
```
```sh
cat > ~/.bash_profile
貼上之後ctrl+c
```

## 添加Library

- [Adding a Library Project](https://www.youtube.com/watch?v=TForBjArUNg)

- [Adding a Jar library](https://www.youtube.com/watch?v=fHEvI_G6UtI)


## 快速鍵

```java
//自動完成不分大小寫
editor>general>code completion的case sensitive completion要調為none

//自加
cmd IKJL : 上下左右
cmd+sft J/L : to line start/end
cmd+sft I/K : extend/shrink selection
cmd p : parameter info

cmd ;或z : Basic/ in Code Completion 函數自動完成 (api doc)
cmd , : insert live template 插入樣板

//mouse
cmd click : Declaration/ in Main menu Naviagte 查原始碼 (或xml)


//視窗檔案切換
ctrl tab

//游標
opt 左右 : 左右移 擴大/縮小選取
sft opt : 上/下移動行
sft enter : end + enter
cmd enter : enter + 上 (游標位置不動產生一空行)


//快速產生程式碼 souf fori logi 
cmd j : 插入樣板
cmd opt t : if / try / while

//開啟燈泡內的選項
opt enter

//查詢物件的method/field 或任何可能的字串
ctrl space : 查詢method/field (選定後按tab!!!!) 或 提示字串自動完成

//建立method
cmd n : 各式選單
ctrl o : select override/implement

//單純查詢
ctrl j : 查詢文件定義
cmd p : 顯示參數定義 ****
cmd b : 查原始碼 (或xml)
cmd u : 跳到父類

cmd opt L : 自動格式化
cmd F9 : 編譯執行

//搜尋內容或檔案
sft sft

sft F6 : 變數改名

cmd + d : 複製貼上一整行 或貼上已選取的內容

cmd sft a : 開啟系統功能搜索


```
