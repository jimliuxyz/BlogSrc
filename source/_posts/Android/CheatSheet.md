---
title: Android CheatSheet
date: 2018-04-04 15:58:09
tags:
  - Android
  - Cheat Sheet
---

```java
//下載圖片
img = Drawable.createFromStream(new URL(url).openStream(), "");

//顯示短暫訊息
Toast.makeText(this, "...", Toast.LENGTH_SHORT).show();

getResources().getStringArray(R....);
getResources().getXml(R....); //XmlResourcesParser
getAssets().open("...")
getSystemService(Service...)

//URI
MediaStore.Images.Media.INTERNAL_CONTENT_URI


```

## 查看activity back stack

```sh
adb shell dumpsys activity activities | sed -En -e '/Running activities/,/Run #0/p' | sed -En -e '/com.jimliuxyz/p'
```
