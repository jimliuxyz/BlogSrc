---
title: Activity
date: 2018-04-04 15:44:29
tags: Android
---

![](https://litotom.com/wp-content/uploads/2017/04/xA5401.png.pagespeed.ic.3u4LcNGfRw.webp)
![](https://i.stack.imgur.com/fRxIQ.png)

![](http://frodoking.github.io/img/android/complete_android_fragment_lifecycle.png)


## Activity重建與復用
[ref](https://www.jianshu.com/p/36124f9793fa?utm_campaign=maleskine&utm_content=note&utm_medium=pc_all_hots&utm_source=recommendation)
>畫面旋轉 或 變更語系 造成Activity重建 連帶影響狀態或物件需要重建

#### Bundle
與Map類似 但key必須是string 用來存放資料

#### 1. Minifest.xml中抑制重建
>在minifest加入android:configChanges提示為自行處理 使Activity不進行重建

```java
//android:configChanges="locale|layoutDirection"
@Override
public void onConfigurationChanged(Configuration newConfig) {
    super.onConfigurationChanged(newConfig);
}
```
#### 2. onRetainCustomNonConfigurationInstance 與 getLastCustomNonConfigurationInstance

>複寫onRetain將稍後要還原內容交給系統 被重建後再取回

```java
@Override
public Object onRetainCustomNonConfigurationInstance() {
    return netWorkTask;
}
```
#### 3. Retain Fragment (官方建議使用)
>設定setRetainInstance(true)即可讓Fragment復用不重建(app狀態須寫在Fragment中) (之後需注意activity/context的更動)

```java
//以下code看似在復用MyFragment,但事實上MyFragment仍會在背景被建立
//除非fragment設定setRetainInstance(true)
if(getSupportFragmentManager().findFragmentByTag(ID) == null) {
    getSupportFragmentManager().beginTransaction().add(new MyFragment(), ID).commit();
}
```

## View狀態的save與restore

1. view需要設定id才能被儲存
2. android:saveEnabled 預設已為true

