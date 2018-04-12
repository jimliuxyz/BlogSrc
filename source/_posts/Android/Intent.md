---
title: Intent
date: 2018-04-04 15:59:41
tags: Android
---

## 概述
> 一個意圖/命令的容器 
> putExtra僅能傳遞native type,或序列化物件(Serializable或Parcelable)

- 顯示 : 指定元件的類別
- 隱式 : 僅提供ACTION/CATEGORY/URI 不指定元件的類別


```java
intent = new Intent(Intent.ACTION_VIEW);
intent.setDataAndType(Uri.fromFile(file), "image/*");
startActivity(intent);

intent = new Intent(Intent.ACTION_VIEW, Uri.parse("http://..."));
startActivity(intent);

```

## Intent導航與返航
####導航

```java
//啟動
startActivity(intent)
startActivityForResult(intent, req_id) //透過activity或fragment呼叫

//被啟動
override fun onCreate(savedInstanceState: Bundle?)  //首次啟動
override fun onNewIntent(intent: Intent?)   //已啟動 但有新intent
```
####返航

```kotlin
setResult() //均透過activity

override fun onActivityResult(int requestCode, int resultCode, Intent data)
```
> fragment有startActivityForResult與onActivityResult,但沒有setResult

#### PendingIntent
> 封裝執行intent的機制 類似於將startActive/startService與intent封裝起來 丟給系統 透過系統機制讓user觸發intent此外尚有些flag策略 通常就是和Notification合用


#### Notification
> 位於手機上方的通知欄 可作為service的ui使其能顯示狀態與被使用者操作

```java
//NotificationManager
nm = (NotificationManager)getSystemService(Context.NOTIFICATION_SERVICE);

//Intent
intent = new Intent(this, MainActivity.class);
intent.setFlags(Intent.FLAG_ACTIVITY_CLEAR_TASK);

//PendingIntent
pi = PendingIntent.getActivity(this, 0, intent, PendingIntent.FLAG_ONE_SHOT);

//NotificationCompat.Builder
builder = new NotificationCompat.Builder(this, "practice_channel")
        .setSmallIcon(android.R.drawable.ic_dialog_info)
        .setContentTitle(getString(R.string.time_to_practice))
        .setContentText(getString(R.string.it_is_time_to_practice))
        .setAutoCancel(true)
        .setContentIntent(pi);

//產生與取消
nm.notify(NOTIFICATION_ID, builder.build());

nm.cancel(NOTIFICATION_ID);
```