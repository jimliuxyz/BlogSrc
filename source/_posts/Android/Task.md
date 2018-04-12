---
title: Task
date: 2018-04-04 16:08:38
tags: Android
---

## Activity.runOnUiThread
> activity內建的一方法 便於異步切回main thread

## AsyncTask
[ref](https://blog.csdn.net/carson_ho/article/details/79314325)
> aad : Execute a background task inside of a Service

- thread pool+handler應用的再封裝 易於與main(UI) thread切換
- 需在main thread建立與執行 (最終記得cancel)
- 如同Thread一次性使用 結束後無法重新啟動

```java
class MyTask extends AsyncTask<String , Integer , Bitmap>{
//<傳入型態, 進度值型態, 結果型態>
    @Override
    public Bitmap doInBackground(String ...args){
        //進入異步 不要做view操作
        publishProgress(...);
        
        activity.runOnUiThread
    }
    onPreExecute() //回main thread
    onProgressUpdate(Integer... values) //回main thread
    onPostExecute(Bitmap bitmap) //回main thread
}

new MyTask().execute("str");
```

## CountDownLatch
```java
// 1.設定好要count down的數量
final CountDownLatch countDownLatch = new CountDownLatch(1);

// 2.在異步thread結束後countDown使其歸零
countDownLatch.countDown();

// 3.一直等到countDownLatch為0才返回
countDownLatch.await();
```

## Timer & TimerTask
```java
Timer timer = new Timer()
timer.schedule(new TimerTask() {
    @Override
    public void run() {
        //...
    }
}, 1000, 1000);
timer.cancel()
```

## Looper + Handler
> Looper消息接收器 建立時連帶建立一個MessageQueue

> Handler消息處理/發送器

> 用途在於將msg或task隊列執行 

> Looper與Handler內部都是利用current thread做為key 所以建立與呼叫時所處的thread很重要

```java
//自訂的
class LooperThread extends Thread {
    public Handler mHandler;

    public void run() {
        //以當下thread建立looper (內部會存於map中)
        Looper.prepare();

        //以當下thread建立Handler 並取得當下thread的looper中的MessageQueue
        //於是looper與handler都有共通的msgQueue
        //之後外部可用handler建立傳遞msg進入msgQueue
        //loop即能收到msg並透過msg的callback回叫handler
        mHandler = new Handler() {
            public void handleMessage(Message msg) {
            }
        };
        
        //以當下thread執行loop (當下thread會進入等待msg的loop而block住)
        Looper.loop();
    }
}
```

#### MessageQueue
> 內部是透過native code實現主要機制

#### HandlerThread
> 同上方範例 但沒有handler

```java
public void download2(String url) {
    HandlerThread ht = new HandlerThread("MyHandlerThread");
    ht.start();//必須先啟動thread (執行prepare後looper才會建立)

    //直接指定Handler的looper
    Handler handler = new Handler(ht.getLooper()) {
        @Override
        public void handleMessage(Message msg) {
            //H:MyHandlerThread
            System.out.println("H:"+Thread.currentThread().getName());
            if (msg.obj=="quit")
               getLooper().quit();
        }
    };

    handler.post(new Runnable(){
        public void run(){
            //R:MyHandlerThread
            System.out.println("R:"+Thread.currentThread().getName());
        }
    });
    handler.obtainMessage(0, "url").sendToTarget();
    handler.obtainMessage(0, "quit").sendToTarget();
}
```
> main thread預設已執行prepare 所以在main thread下Handler能直接使用
