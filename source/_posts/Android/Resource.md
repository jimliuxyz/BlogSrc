---
title: Resource
date: 2018-04-04 18:38:33
tags: Android
---


## 資源引用出現的樣式
```java
[@|?][namespace:]type/name

//type : string，dimen，drawable，layout，color，anim，animator，mipmap，raw，id，style，attr，array

//@ : 引用切確資源
@android:string/title   //Android本身的資源
@string/title           //省略namespace即引用自建資源

//? : 引用自主題屬性 (如果主題沒定義該屬性會報InflateException)
android:background="?attr/colorPrimary"

//+ : 通常是替自己建立資源設定ID時使用
android:id="@+id/myViewId"

//@null : 應該是一個特別的id 讓某些屬性能夠不執行
android:button="@null"
android:background="@null"
```

## assets與raw
> assets不會產生id於R資源 可有目錄結構 透過AssetManager讀取

> raw屬於R資源 透過R.id讀取

```java
AssetManager am = getAssets();  
InputStream is = am.open("filename");  

InputStream is = getResources().openRawResource(R.id.fileNameID);

VideoView.setVideoUri(Uri.parse("android.resource://" + getpackageName() + "/" + R.raw.movie));
```

## 多國語言
只需透過IDE編輯即可 (勾選不需要翻譯的字串)


Drawable
======
> 可被畫出在View上的 例如圖檔來源 from BitmapDrawable

> 也可從xml定義

- color
- shape : 形狀 方圓線等
 - stroke 描邊
 - solid 填充
 - corners 邊角
 - padding
- selector : 狀態 每個item搭配一個狀態對應一個drawable
 - state_pressed / state_focused / state_selected / state_checkable
 - state_checked/ state_enabled / state_activated / state_hovered
 - state_window_focused
- layer-list : 圖層 越先執行的在越底層 用很多層疊出一個drawable

