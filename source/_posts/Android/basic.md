---
title: Android環境概念
date: 2018-04-04 15:13:31
tags: Android
---

------
編譯流程
======
- Dalvik : Android的JVM
- dex : 類似jar
- appt：Android Asset Packaging Tool

![編譯流程](https://img-blog.csdn.net/20161122102458470)

------
Minifest
======
[Minifest-activity參數](http://www.wenwenti.info/article/856135)

------
Context
======

[ref](https://www.jianshu.com/p/fb0681f5f12b)

 <img src="https://upload-images.jianshu.io/upload_images/1311456-5ca2e2152180d6aa.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/623" width = "400" alt="" />


> context可理解為app的執行環境 (包含狀態與工具)

> ContextWrapper會包含一個Context實例(mBase)達到複製Context

> Application與Activity與Service雖都能當context用(但非對應同一個mBase) 要注意Activity與Service是有被回收的可能 避免將其當context傳遞 但某些關聯性的動作只能使用activity(像是show dialog)

```java
//若context的傳遞會被對方持有時 盡可能使用Application的context
context.getApplicationContext()
```


------
Application
======

> 繼承Context 可以理解為另外一個層級的執行環境
> 透過Manifest指定自訂的Application類

```xml
<!--Manifest-->
<application android:name=".CustomApplication">
```

------
SharedPreference
======
key/value pair的儲存器 用於儲存app的設定值

```java
MODE_MULTI_PROCESS
MODE_PRIVATE

sp = PreferenceManager.getDefaultSharedPreferences(context);
sp = getSharedPreferences("mysp", Context. MODE_PRIVATE);
sp.getBoolean("key", false);
sp.edit().putBoolean("key", false);
```