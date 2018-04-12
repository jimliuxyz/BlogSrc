---
title: Broadcast
date: 2018-04-04 19:59:35
tags: Android
---

## Broadcast
> 發送或接收系統事件廣播 如低電量/簡訊
> 生命週期在onReceive()結束後終止
> 以靜態方式(manifest)註冊接收器 可以不用開app 也能接收事件

```java
registerReceiver(new BroadcastReceiver(){}, new IntentFilter("action..."));
unregisterReceiver(mybc);

Intent intent = new Intent(this, MyBC.class)
Intent intent = new Intent("action...")
intent.putExtra("data", "mydata");

sendBroiadcast(intent);

class MyReceiver extends BroadcastReceiver{
    onReceive(content, intent);
}
```
或以常駐的方式(不論app是否啟動或kill都能被執行)

```xml
<receiver android:name=".MyReceiver">
    <intent-filter>
        <action android:name="android.intent.action.EDIT" />
    </intent-filter>
</receiver>
```