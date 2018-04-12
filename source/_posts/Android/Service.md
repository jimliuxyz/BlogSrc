---
title: Service
date: 2018-04-02 17:34:13
tags: Android
---

![](https://upload-images.jianshu.io/upload_images/3881353-66a45f88bedec256.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

## Service
- app透過startService啟動service
- 執行於UI Thread
- 不會自動被系統結束 只能自己透過stopSelf結束

#### onStartCommand的回傳決定Service被系統殺掉後的行為

> START_STICKY : Service被殺掉, 系統會重啟, 但是Intent會是null。

> START_NOT_STICKY : Service被系統殺掉, 不會重啟。

> START_REDELIVER_INTENT : Service被系統殺掉, 重啟且Intent會重傳。 



## Remote Service
- 同為Service僅是啟動/取得方式不同 主要用於跨process的應用
- 即透過bindService(intent, conn, BIND_AUTO_CREATE)取得binder實例操控service
- 以bindService啟動service會造成Service綁定於App的生命週期
- 用AIDL建立共通介面
- 內建已經做好了一個共通的binder 可以透過(Message(msg)).getBinder()建立 基本上是用handler處理message的方式傳遞訊息 (就不用建AIDL了)
- android:process屬性 : 可讓Service執行於自有的process上(remote service) 通常會設為:remote(這似乎屬於一個自訂的命名)

## Binder
> 在android中做process間通訊用 (底層機制應該是linux的share memory)

#### AIDL
[ref](https://blog.csdn.net/u011240877/article/details/72765136)
> Android Interface Definition Language
> 一種程式語言 用於定義介面 用於IPC(Process間通信) 與java的interface寫法類似

```java
//IRemoteService.aidl
package com.example.service;

interface IRemoteService{
    int getData(String key)
}
```

> 存成MyDataBinder.aidl,會被自動轉譯產生一個java檔,產生IRemoteService.Stub類別,作為server與client間共通的介面

> server端實做IRemoteService.Stub的method即IBinder 並在onBind時回傳

> client端也必須有該.aidl檔才能編譯 並透過IRemoteService.Stub.asInterface將IBinder轉為代理物件供使用

## Remote Service範例

#### 實做介面
```java
//RemoteService.java
public class RemoteService extends Service {
    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Return the interface
        return mBinder;
    }

    private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
        public int getData(){
            return "hello";
        }
    };
}
```
---
#### start service & bind
```java
//Activity.java
{
    Intent intent = new Intent(Binding.this, RemoteService.class);
    intent.setAction(IRemoteService.class.getName());
    bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
}

IRemoteService mIRemoteService;
ServiceConnection mConnection = new ServiceConnection() {
    public void onServiceConnected(ComponentName className, IBinder service) {
        mIRemoteService = IRemoteService.Stub.asInterface(service);
    }

    public void onServiceDisconnected(ComponentName className) {
        mIRemoteService = null;
    }
};
```

## IntentService
> Service的子類 需在manifest中宣告

> Service+Handler+Looper的封裝 即類似一個task queue 當所有任務執行完時銷毀 有任務再重新建立

```java
public class IntentServiceDemo extends IntentService{
    public IntentServiceDemo(String name){ //name for HandlerThread
        super(name);
    }

    @Override
    protected void onHandleIntent(Intent intent){
        //進入異步
    }
}

Intent intent = new Intent(this, IntentServiceDemo.class);
startService(intent);
```

## JobService / JobScheduler
[ref](https://blog.csdn.net/qq_33689414/article/details/54668889)
[android-JobScheduler](https://github.com/jimliuxyz/android-JobScheduler)
> aad:Schedule a background task using JobScheduler

> Service的子類 排程不緊急或與設備狀態有關的任務(受系統牽制) 以優化系統電力消耗

> 在(>=Build.VERSION_CODES.N)中限制了interval最小為15m flex最小為5m 比較適合惰性的任務 (時間不會準確)

- JobScheduler : 整個app僅有一個JobScheduler實體用來排程任務(丟入JobInfo)
- ComponentName : 一個pkg+cls用以描述元件 Component泛指四大組件 常用於Intent
- JobInfo : 指定任務執行模式 依時間/網路/Idel/充電 (透過Builder模式構建) 儲存extras當param給JobService執行
- JobService : 當onStartJob時得到任務 回傳false表示任務已執行結束 回傳true表示任務進入異步執行 完成後須自行呼叫jobFinished

#### 意象:
> JobService如同Service般執行 經由JobInfo建立不同任務的參數 由JobScheduler指派執行時機 呼叫JobService.onStartJob執行

> 執行於ui thread,耗時任務仍需另使用AsyncTask

```xml
<service
    android:name=".service.MyJobService"
    android:permission="android.permission.BIND_JOB_SERVICE"
    android:exported="true"/>
```
```java
JobScheduler tm = (JobScheduler) getSystemService(Context.JOB_SCHEDULER_SERVICE);
```
> onStopJob僅在系統強制要停止任務時呼叫

## AlarmManager
[ref](https://www.jianshu.com/p/fdb4e8c009b7)
> aad:Schedule a time-sensitive task using alarms

> 安排定時任務(time-sensitive task) 時間到用PendingIntent開啟服務或Activity

> 比起Java SE的Timer, AlarmManager能夠喚醒休眠的Android系統(透過PendingIntent)

> 被限制了interval最小為60s (時間不會準確)

```java
val intent = Intent(this, AlarmReceiver::class.java)
val pi = PendingIntent.getBroadcast(context, 0, intent, PendingIntent.FLAG_UPDATE_CURRENT);

val am = getSystemService(Context.ALARM_SERVICE) as AlarmManager

am.set(AlarmManager.RTC_WAKEUP, System.currentTimeMillis(), pi)
am.setRepeating(RTC_WAKEUP, System.currentTimeMillis() + 5 * 1000, 3 * 1000, pi);
am.setRepeating(ELAPSED_REALTIME_WAKEUP, SystemClock.elapsedRealtime() + 1000 , 5 * 1000 , pi);

class AlarmReceiver : BroadcastReceiver() {
    override fun onReceive(context: Context, intent: Intent) {
    }
}
```
```xml
<receiver
    android:name=".AlarmReceiver"
    android:enabled="true"
    android:exported="true"></receiver>
```

#### type(組合)
- RTC : 以系統時間判斷 (也就是說系統時間更改將立即反應到鬧鐘上)
- ELAPSED_REALTIME : 以系統流逝的時間差判斷
- WAKEUP : 能於睡眠中喚醒
- POWER_OFF_WAKEUP : 能於關機中喚醒

