---
title: 協助工具
date: 2018-04-04 15:55:53
tags: Android
---

## uiautomatorviewer
> 檢視View階層 (方面找ID)

```sh
cd ~/Library/Android/sdk/tools/bin/ uiautomatorviewer
```

## draw9patch
> 製作不受縮放影響畫質的按鈕外框圖樣 (避免四角被伸縮)

## 查詢TaskStack
[adb shell dumpsys](https://www.jianshu.com/p/d859480613a4)
```sh
adb shell dumpsys
```

## Android-Debug-Database
[ref](https://github.com/amitshekhariitbhu/Android-Debug-Database), [本地端連結](http://localhost:8080/#)

```xml
dependencies {
    debugImplementation 'com.amitshekhar.android:debug-db:1.0.0'
}
```
```sh
# Working with emulator
cd /Users/jimliu/Library/Android/sdk/platform-tools
./adb forward tcp:8080 tcp:8080
```

## LeakCanary
[LeakCanary](https://github.com/jimliuxyz/leakcanary)
> 檢測記憶體洩漏 (針對activity生命週期)

```xml
dependencies {
  debugImplementation 'com.squareup.leakcanary:leakcanary-android:1.5.4'
  releaseImplementation 'com.squareup.leakcanary:leakcanary-android-no-op:1.5.4'
}
```

```java
public class ExampleApplication extends Application {
  @Override public void onCreate() {
    super.onCreate();
    if (LeakCanary.isInAnalyzerProcess(this))
      return;

    LeakCanary.install(this);
  }
}
```
