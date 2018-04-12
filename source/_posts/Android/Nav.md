---
title: 頁面導航
date: 2018-04-04 16:01:31
tags: Android
---

## task back stack行為設定參考
[官方中文說明](https://developer.android.com/guide/components/tasks-and-back-stack.html?hl=zh-tw)
[Android 深入Activity](https://www.youtube.com/playlist?list=PLO5e_-yXpYLCQNdl6G8RIZLrlBPRQDCWR)
[Android基礎知識](https://www.bilibili.com/video/av11208065/index_11.html#page=1)

1. launchMode
2. intent flag
3. minifest activity

## Deep Link(App Links) 超連結啟動app
[ref](https://developer.android.com/training/app-links/deep-linking.html)
[ref](https://developer.android.com/studio/write/app-link-indexing.html)
> aad:Implement non-standard task stack navigation (deep links)

```xml
<intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.DEFAULT" /> <!--從adb啟動-->
    <category android:name="android.intent.category.BROWSABLE" /> <!--從browser啟動-->
    <data android:scheme="app" android:host="jimliuxyz.com"></data>
</intent-filter>
```
```sh
adb shell am start -W -a android.intent.action.VIEW -d "app://jimliuxyz.com" com.jimliuxyz.tsnote

am : activity maneger
-W : wait for launch to complete
[-a <ACTION>] [-d <DATA_URI>]
package不一定要
```
> 如果要用override fun onNewIntent()處理 activity要為`singleTop`之類

## TaskStackBuilder
[ref](https://www.youtube.com/watch?v=MvIlVsXxXmY)
[ref](https://developer.android.com/training/implementing-navigation/ancestral.html)
> 用於憑空建立parent activity於TaskStack中
> 當MsgActivity是從其他app(intent或deep link)啟動時 並沒有自己的back stack 所以按下back按鈕時 無法回到MainActivity 此時就需要NavUtils工具

#### Minifest
> 指定android:parentActivityName為欲返回的MainActivity

```xml
<activity
    android:name="com.example.myfirstapp.MsgActivity"
    android:label="display_message"
    android:parentActivityName="com.example.myfirstapp.MainActivity" >
    <!-- Parent activity meta-data to support 4.0 and lower -->
    <meta-data
        android:name="android.support.PARENT_ACTIVITY"
        android:value="com.example.myfirstapp.MainActivity" />
</activity>
```

```java
//通常都經由ActionBar左上角的箭頭驅動返回事件(android.R.id.home)
getActionBar().setDisplayHomeAsUpEnabled(true);
```

```java
//測試不ok
Intent upIntent = NavUtils.getParentActivityIntent(this);
if (NavUtils.shouldUpRecreateTask(this, upIntent)) {
    //非處於自己app的back stack中時
    TaskStackBuilder.create(this)
        .addNextIntentWithParentStack(upIntent)
        .startActivities();
} else
    NavUtils.navigateUpTo(this, upIntent);
```

#### 透過PendingIntent預先設定好(測試ok)
[notify-user](https://developer.android.com/training/notify-user/navigation.html)

```java
Intent intent = new Intent(this, MsgActivity.class);

TaskStackBuilder stackBuilder = TaskStackBuilder.create(this);
stackBuilder.addNextIntentWithParentStack(intent); //依manifest設定的android:parentActivityName

pi = stackBuilder.getPendingIntent(0, PendingIntent.FLAG_UPDATE_CURRENT);
```


## launchMode
> manifest可設定activity的android:launchMode

#### standard : 不斷新建堆疊 
```
a - a' - a''
```
#### singleTop : 同standard,但當該activity已處於stack top時不會再新建
```
a - a - a - b - a' - a'
```

#### singleTask : 不會重開,該activity出現時勢必將task中其他task全部拋掉
```
a - a - b - c - d - a [back]離開app
```

#### singleInstance : 不會新建,該activity的task僅允許自己使用
```
a - a -     a [back]離開a回到b
        b -                  [back]離開b回到home
```

## 簡述
- standard - 不斷重開堆疊 : 預設 (可能有多個aty重複)
- singleTop - 頂端不新建 : 合理避免重複 (可能有多個aty重複)
- singleTask - 頂端排他 task pop all : Home
- singleInstance - 獨佔 : 供別的app使用

single指的是activity不新建 但會執行onNewIntent()


## taskAffinity allowTaskReparenting

affinity類似一個groupId的概念 activity預設的affinity為package name
當一個activity以FLAG_ACTIVITY_NEW_TASK啟動時會以affinity去找該被加到哪個task中

## FLAG_ACTIVITY

透過intent開啟的activity該依循何策略進入task或操作stack

- FLAG_ACTIVITY_NEW_TASK(同singleTask) : aty會在自己的task中

```
new aty : 為aty新增task
new aty : push進aty原屬的task
old aty : 顯示task但aty不會到top (怪怪的)
```

- FLAG_ACTIVITY_CLEAR_TOP : pop出原本在其上者

```
b是FLAG_ACTIVITY_CLEAR_TOP
a - b - c - d - b (此時cd會被pop出,剩下ab)
```

- FLAG_ACTIVITY_BROUGHT_TO_FRONT : activity會to front

- FLAG_ACTIVITY_MULTIPLE_TASK : 能以另一個task啟動(act實體個別存在不同的task)

- FLAG_ACTIVITY_SINGLE_TOP(同singleTop)

- FLAG_ACTIVITY_CLEAR_WHEN_TASK_RESET與FLAG_ACTIVITY_RESET_TASK_IF_NEEDED : 1.設定清除點 2.pop掉清除點與之後的

還有很多...
