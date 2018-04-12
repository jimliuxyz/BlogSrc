---
title: JMM (Java Memory Model)
date: 2018-04-06 15:42:02
tags:
    - Java
---

[*ref](http://wiki.jikexueyuan.com/project/java-concurrent/java-memory-model.html) [ref](https://blog.csdn.net/qq_33689414/article/details/73527438) [ref](https://medium.com/@kevingyc/java-java-thread-model-memory-model-22692fc08671)

## JMM (Java Memory Model)

![](https://cdn-images-1.medium.com/max/1600/1*X01rfvldaQ5K0lZwQyvIFg.png)

JVM執行區分 主內存(共享內存) 與 本地內存

- Main : 主記憶體 (heap)
- Local : 各個thread獨立使用 (cache/副本)

thread執行時會將需要的內容從main複製到local 之後操作各自的local memory, thread彼此間是無法得知其他thread操作的結果 除非重新同步main的資料

- local從main讀寫資料應該是視為synchronized或有cpu確保同步

```java
String str = new String("hello");
//str為一參考 會放在stack
//new String會放到heap
//hello是字面值會放到靜態儲存區(應該也是heap)
```
## 可見性(visibility)
> 指A修改變數x時 確保B能夠隨時得到最新的值 (即cache失效)
> Java透過volatile將變數宣告為`易變的/易揮發的` 以提示其需要可見性的維護 (但並沒有原子性保護)

```java
x = 0;
AB兩thread同時執行+3與+5,預期得到8

volatile x = 0;
A(x=x+3) : 0+3=3 將3指定給x
B(x=x+5) : 0+5=5 此時x變為3,但運算已結束 將5指定給x
```
> 最後卻得到5,這是因為x=x+5的動作並不屬於原子操作,x+5與x=?是分別執行的,中間可以穿插,所以volatile不一定能確保變數一定是thread-safe的
> 故volatile一般都用在`狀態或旗標`變數上

## 原子操作 (atomic)
原子是最小無法分割之物 用來形容操作是不能分割的 以完成i++為例 分別需要取值/運算/賦值 假若期間無法插入任何與i相關的操作 就可以確保i的正確性

## 範例

```java
volatile int x = 0;
volatile int y = 0;
AtomicInteger a = new AtomicInteger(0);

for (int i = 0; i < 5; i++) {
    new Thread(new Runnable() {
        @Override
        public void run() {

            for (int i = 0; i < 100000; i++) {
                x = x + 1;
                a.addAndGet(1);
                synchronized (Thread.class){
                    y = y + 1;
                }
            }

            System.out.println("ans x : " + x); //405949 <- 錯的
            System.out.println("ans y : " + y); //500000
            System.out.println("ans a : " + a); //500000
        }
    }).start();
}
```

## CAS

- compareAndSet(expected, new) atomic檢查主入內存與expected是否相同,若相同表示可以賦值為new 否則回傳false
- 猜測應該是用在`鎖`的應用上 類似CountDownLatch 但不會阻塞

```java
atomicI = new AtomicInteger(0);
private void safeCount() {
    for (;;) {
        runCount++;
        int i = atomicI.get();
        boolean suc = atomicI.compareAndSet(i, ++i);
        if (suc) break;
    }
}
```