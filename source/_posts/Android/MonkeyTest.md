---
title: 壓力循環測試
date: 2018-04-04 16:06:51
tags: Android
---

[Android測試](https://www.youtube.com/playlist?list=PLO5e_-yXpYLD_0y8agkLMcYwqegmXFNMD)

## adb (android debug bridge)
Android SDK本地端執行程式

crash / anr(activity not responding)

```sh
//列出device
adb devices

//進入手機shell (如果Permission denied可執行su)
adb shell

//到安裝路徑
cd /data/data/com.jimliuxyz.vocabook.mock/

//安裝apk
adb install package.apk

//取得 device log
adb logcat
```

## monkey
Android OS上的測試程式

## monkey 自動壓力測試(隨機操作)
```sh
//執行測試 (隨機點擊操作)
adb shell monkey 100

//執行100個操作 每1000ms執行一次 以50為操作seed
adb shell monkey -p tw.com.langstar.t1 -v -s 50 --throttle 1000 100

-v : detial info
-s : seed操作種子 固定的種子能複製出同樣的操作行為
--pct-touch : touch操作的百分比
--ignore-crashes : crash後自動重啟再測試

---

//手機內存放anr的log目錄
data/anr
```

## monkey 自訂測試腳本

```sh
//Android SDK內建的UI元件檢視器 能查詢執行中的app
/Users/jimliu/Library/Android/sdk/tools/bin/uiautomatorviewer

//上傳檔案
adb push myscript /data/local/tmp

//執行測試 (隨機點擊操作)
adb shell monkey -f /data/local/tmp/myscript 100

```

```sh

// 點擊
DispatchPointer(downtime,eventide,press:0/release:1,x,y,pressure,size,metastate,xpresision,ypresision,device,edgeflags)DispatchPointer(10,10,0,100,100,1,1,-1,1,1,0,0)

DispatchString(mystr)

//manifest的activity須加exported="true"屬性
LaunchActivity(package, package + ActivityName)

UserWait(1000) //ms

DispatchPress(66) //keycode

```

## monkeyrunner
Android SDK本地端程式
/Users/jimliu/Library/Android/sdk/tools/bin/monkeyrunner
從本地端執行python控制測試流程 可抓圖

```py
from com.android.monkeyrunner import MonkeyRunner,MonkeyDevice,MonkeyImage
#MonkeyRunner.alert("msg", "title", "okText")

device = MonkeyRunner.waitForConnection(3, "emulator-5554")

device.startActivity("package/package.activityname")

MonkeyRunner.sleep(2)

device.touch(100, 100, "DOWN_AND_UP")

device.type("string")
device.press("KEYCODE_ENTER", "DOWN_AND_UP")

image = device.takeSnapshot()
image.writeToFile("./test.png", "png")

```

```sh
monkeyrunner demo.py
```

