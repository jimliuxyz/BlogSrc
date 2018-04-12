---
title: Thread
date: 2018-04-04 16:41:05
tags: Java
---

相關知識
======

- program : binary code,一段機械碼或可被執行的檔案
- process : 執行一個program後形成一個process,其中包含一塊記憶體空間(code/heap). process間是不共用的,互相通信需特殊的機制
- thread : 實際執行程式碼的單元 包含stack與cache儲存變數與參考
- task : 任務,軟體概念,如同Runnable介面
- yield : 類似sleep 但不能指定時間 目的在於讓出cpu時間給`同優先級`的執行緒 (針對非timeslicing作業系統)

# Concurrency(並發) vs Parallelism(並行)
> 與thread數量無絕對關連 與資源共用有關
> 資源可以指`時間`或`執行緒`或`資料物件`
> 例如javascript中共享執行緒的`異步執行(協程)(Promise)`就屬於併發的應用

![Concurrency vs Parallelism](https://cdn-images-1.medium.com/max/409/1*_4B2PKsJn9pUz3jbTnBnYw.png)

![](https://pic1.zhimg.com/80/4733c03cd0e126b9a500d5912bf9b581_hd.jpg)

- 並發 : 一個資源 輪流供應多個人使用 (共用需要使用`鎖`)(有能力處理一起發生的事件,但未必一起執行)
- 並行 : 每個人有各自的資源 雖同時進行 但互不相干

Thread
======
![](http://wiki.jikexueyuan.com/project/java-interview-bible/images/thread.jpg)
> 其應用通常與Lock息息相關

#### Daemon Thread
> 守護線程 又稱background thread 其生命週期中斷於程式的其他thread全部結束 例如GC線程
> setDaemon(true)

corutine
======
> 協程 由編譯技術實現 被視為輕量級thread 應該是依附在thread 用於異步執行

> 透過async/await讓工作不立即執行 且能夠被等待 相當於排程的概念 更有效率的使用thread

例如 : 在main thread中連續執行ABC三函數
- 一般情況下執行的順序是ABC
- 若B能進入異步 順序會變為AC...B (B不確定何時執行)
- 若B能進入異步 且C能等待B執行完 順序又變回ABC
- 假若B在異步執行時是透過其他thread 那C等待B時main thread還能繼續其他工作

```js
async{
    value = await getData();
}
```

Thread Pool
======

[ref](http://wiki.jikexueyuan.com/project/java-concurrent/thread-pools.html)
- ExecutorService 即thread pool的實現
- Executor介面類 即execute() 供ExecutorService實現
- Executors工具類 便於產生ExecutorService
- 內部有一個BlockingQueue存放任務
- 每個thread如同一個looper等待取得task


Thread的切換
======

一個流程中某些動作必須跳到異步執行 待執行完再切回原本的thread 這些切換thread的動作將使一個流程無法順暢的寫完

舉例:

1. user按下執行按鈕 (ui)
2. 下載url的內容 (network)
3. 存成本地檔案 (i/o)
4. 取得執行狀況 將內容顯示於ui (ui)


## message handler
> 適合簡單的切換 多個連續流程仍難以處理

#### 範例
- 切換越多次巢狀越明顯

```java
public class AppExecutors { //seeu 建立Thread切換機制
    private static final int THREAD_COUNT = 3;
    private final Executor diskIO;
    private final Executor networkIO;
    private final Executor mainThread;

    public AppExecutors() {
        diskIO = Executors.newSingleThreadExecutor();
        networkIO = Executors.newFixedThreadPool(THREAD_COUNT);
        mainThread = new MainThreadExecutor());
    }

    public Executor diskIO() {
        return diskIO;
    }

    public Executor networkIO() {
        return networkIO;
    }

    public Executor mainThread() {
        return mainThread;
    }

    private static class MainThreadExecutor implements Executor {
        private Handler mainThreadHandler = new Handler(Looper.getMainLooper());

        @Override
        public void execute(@NonNull Runnable command) {
            mainThreadHandler.post(command);
        }
    }
}

```

ForkJoinPool
======

專處理可併行的任務 將任務(ForkJoinTask)切割執行於pool中
> 運用於Collection.ParallelStreams中

```java
//ForkJoinTask大致寫法

@Override
protected Integer compute() {
    if(needSplit){
        return sum();
    }else{
        int middle = (start + end) / 2;
        CountTask task1 = new SumTask(start, middle);
        CountTask task2 = new SumTask(middle+1, end);
        
        //執行分割任務
        task1.fork();
        task2.fork();

        return task1.join() + task2.join();
    }
}
```