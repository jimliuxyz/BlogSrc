---
title: Gradle
date: 2018-04-04 19:10:47
tags: Android
---

[Groovy 語言特性介紹](http://cloudchen.logdown.com/posts/248361/features-of-groovy-for-gradle-beginner)

![](http://cl.ly/image/2P2m0j3z3i2J/Image%202015-01-06%20at%2010.53.33%20AM.png)


## 加快編譯

```
//gradle.properties
org.gradle.jvmargs=-Xmx512m
org.gradle.daemon=true
org.gradle.parallel=true
org.gradle.configureondemand=true

```
## 異常"Gradle project sync failed"

在android sample中加入kotlin一直不成功 但new project中卻沒問題

把code移入new project中確認也沒問題 可見是環境造成

> rm -rf .gradle/ .idea/ build gradle

> 最後是把src root folder從Application改名(Refacotr)為app才正常

## buildTypes
> 控制編譯流程 debug / release

## productFlavors
> 分歧編譯目錄 mock / prod 不同選擇即用不同目錄下的檔案編譯

- flavorDimensions : 對flavor再進行一次分類 例如platform,vertype 組合出多個目錄

## BuildConfig
設定會透過BuildConfig類帶入java中

## applicationId
作用在google play store上 用以辨識app, 不同的release要有不同的applicationId, 否則會被視為同一個app

可寫在defaultConfig或個別的buildType/productFlavors中 也可用applicationIdSuffix

