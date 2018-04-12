---
title: Lock
date: 2018-04-06 15:42:02
tags:
    - Java
---

[ref](http://wiki.jikexueyuan.com/project/java-concurrent/locks-in-java.html)

同步與異步
======
`同步`二字在生活上通常都解釋成`同時進行/亦步亦趨` 但程式上不是這樣觀念
- 異步 : `各自行動 互不等待` (記住異步的定義)
- 同步 : 行動須受牽制 等待 使一致

比喻
- 異步 : 像是短跑比賽 參賽者各跑各的
- 同步 : 像是跑接力賽 沒拿到接力棒就只能等待 (執行上依賴共享的資源)

用途
- 異步 : 增進執行效率
  - 多執行緒 : 使用共享資源時還是要進入同步
  - 單執行緒 : 任務寫在Event Callback/Hook 由Event Loop驅動 仿效異步 (如JavaScript)
- 同步 : 保護共享資源的必要手段


## 異步執行
> 指耗時任務交付後就可以先去做別的事情 等任務執行完再通知
> 此任務稱為異步任務 不會馬上被執行
> 架構為事件處理型(onEvent)的設計
> 異步任務(Runnable)可以用thread或handler達成


## 同步執行
> 指事情必須依序處理 未完成前不能離開
> 例如從網路取得一張圖片 顯示在螢幕上 若下載圖片要10秒鐘 程式就必須等待完成後才能繼續執行
> 程式的基礎都是建立在同步執行上的 而多執行緒中的同步更強調的是`只有一個執行緒在執行該段程式`
> 執行緒競爭要達到同步的代價就是`等` 等的實踐就是`鎖` 鎖的用途就是`保護`

> 對於`鎖或與執行緒同步`相關的實踐:
 - synchronized
 - lock
 - blocking queue
 - semaphore


## 需要等待的情境
- 等待共享資源 (用同步鎖解決)
- 等待異步任務(一個或多個)結果 (Thread.join或更高階的手段)


鎖
======
- 臨界區(critical section) : 即被鎖住的區塊
- 可重入性(reentrant) : 指thread可以重入相同的鎖中 即在funcA中又進入funcB
- 線程飢餓與公平(Starvation and Fairness) : 指線程搶不到開鎖機會的狀況
- Busy Wait : 指while loop的等待方式 造成cpu持續忙碌而無法有效利用

## synchronized
- 以某個物件(實例或類)做為鎖 多個thread中僅能一個thread取得鎖 也就是執行權
- 確保僅有一個thread進入critical section
- 確保從main memory讀入 離開時寫回
- thread間取得鎖的結果是不公平的 有優先權的 而不是先到先贏

```java
class Test{
    //實例鎖
    public synchronized void funcA(){ }
    public synchronized void funcB(){ }

    //類鎖
    public static synchronized void func(){ }

    static obj...
    public void func(){
        //實例鎖
        synchronized(this){ }

        //類鎖
        synchronized(obj){ }
    }
}
```

## 線程間能被安全共用的實例

```java
public class MyData{
  protected Object data = false;
  public synchronized Object get(){
    return data;
  }
  public synchronized void set(Object data){
    this.data = data;
  }
}
```

## 信號機制
> 每個Object都能做為MonitorObject
- wait : 使當下thread進入等待 並監視MonitorObject的信號
- notify : 喚醒監視MonitorObject的thread (由系統決定喚起的thread)
- notifyAll : 喚醒全部監視的thread 重新競爭鎖

> Missed Signals : 指notify前沒有相應的wait先被執行 而可能造成之後的wait無人喚醒 算是設計錯誤

## 以信號機制實現鎖
```java
public class Lock{
    private boolean isLocked = false;

    public synchronized void lock() throws InterruptedException{
        //isLocked為false時 進入wait循環 thread被鎖住 等待另一個thread開鎖
        //若同一個thread未unlock又來lock第二次 同樣會被鎖死 造成沒有thread能解鎖
        while(isLocked){
            wait();
        }
        isLocked = true;
    }

    public synchronized void unlock(){
        isLocked = false;
        notify(); //相當於觸發信號
    }
}

public class Counter{
    private Lock lock = new Lock();
    private int count = 0;

    public int inc(){
        lock.lock();
        int newCount = ++count;
        lock.unlock();  //若臨界區會拋出異常 需使用catch finally
        return newCount;
    }
}
```
> wait如同sleep 但從wait中醒來並不是一定要有notify 它僅是告知已經可以從wait中喚醒 這種不是經由notify喚醒的情形稱作`虛假喚醒(spurious wakeup)` 故wait與notify之外仍需要isLocked做為是否unlock的判斷

> 這種while建構的鎖稱為自旋鎖

> 實現`可重入性`要考慮:
 - 記住是由哪個thread上鎖 且上鎖幾次
 - 解鎖必須counter歸零

#### Slipped Conditions
> 即指while(isLocked)與isLocked=true非一氣呵成(原子操作) 執行的間隙造成鎖的狀態改變

## FairLock
> 透過List將記住請求鎖的順序

## Semaphore
> 計數信號 實作上首先要確保操作的原子性
> 當資源量為1時 可當鎖用
- SEM_VALUE_MAX 資源量
- P take/wait 減1操作 取得資源
- V release/signal 加1操作 歸還資源

## BlockingQueue
> 阻塞隊列 當take時沒有元素 或put時沒有空間時阻塞線程

