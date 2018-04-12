---
title: Collection
date: 2018-04-06 15:42:02
tags:
    - Java
    - Collection
---

[ref](http://jax-work-archive.blogspot.tw/2015/02/java-setlistmap.html) [ref](http://blog.oneapm.com/apm-tech/650.html)

類型
======

![](https://lh3.googleusercontent.com/Zymadzpwi3aKAlI1EpBjbfudYBuZuam7d6xWRia7OZqVZya7br-b0LdeykU5db-7i6WIUxymQ_OaV7nf12y1RPv4aC_IPsIfd4Vel4qeWVpXK10Rkhm4sZxq5jVt4aLkc2CzdNyP)

## Collection
> 一無序物件群體的介面

## Set
> 一無序物件群體 元素無法重複(會覆蓋) 沒有索引
> 沒有get方法 因爲沒有key的概念 僅存放value(或稱element) 用iterator遍歷
> HashSet為常用的實現 或可排序的TreeSet

## List
> 一有序物件群體 元素可重複 有數字索引 能get(idx)

## Map
> 鍵值對 (Key-Value pair) (entrySet)

## 結論

- 要數字索引 : List (ArrayList) (CopyOnWriteArrayList)
- 有Key-Value關聯 : Map (HashMap/LinkedHashMap) (ConcurrentHashMap)
- 單存放置一群物件 : Set (HashSet)
- 要隨時排序 : TreeMap

> 基本上如此 但技術演變很快 類別也會出現變形

- concurrent(指多thread能夠同時存取) 意味 thread-safe

## 遍歷

```java
HashMap<Object, Object> map = new HashMap<Object, Object>();

//keyset
for (Object k : map.keySet()){
    System.out.print(k + " ");
}

//entryset
for (Map.Entry entry : map.entrySet()) {
    System.out.print(entry.getKey() + " ");
}

//iterator
Iterator entries = map.entrySet().iterator();
while (entries.hasNext()) {
    Map.Entry entry = (Map.Entry)entries.next();
    System.out.print(entry.getKey() + " ");
}
```

> 測試發現 : HashMap與Hashtable 遍歷時都是沒有規律的


實現類
======
[實現解說](http://wiki.jikexueyuan.com/project/java-collection/)

## HashMap (ConcurrentHashMap)
> 以hashcode為key (null也有其hashcode)
> 速度快,遍歷時無序的(非照put時機或key值排列)
> 非thread-safe, multi-thread資料可能異常,但有ConcurrentHashMap可以替代,或Collections.synchronizeMap(map)使其thread-safe
> 需要預留較大的array空間(對應每個hashCode) 資料量不多時划不來
> 以一個hash值為索引的array其每個元素對應到一個list 當list長度超過8則改為紅黑樹(自平衡二元樹)

## Hashtable
> thread-safe, 慢
> 繼承Dictionary
> 較早的技術 建議用HashMap取代

## LinkedHashMap (小資料量的通常的選項)
> 繼承HashMap (應該是以雙向鏈取代固定size的array)
> 遍歷時是`依照put順序的`
> 較慢 但啟動較省空間 在資料不多時 不見得比較慢

## EnumMap
> 以enum為鍵 意即使用時就`已知最大容量` 通常會較快

## TreeMap
> 實做SortMap介面
> 遍歷預設`依照key值的升序排序` 可以自訂Comparator

## WeakHashMap
> 弱參考 可做自動回收的快取

## HashSet
> 以HashMap實現
> 即以Object的hashCode為key 本身為值 故同樣的hashCode無法對應兩個值 即不會重複

```java
map.put(key, new WeakReference(value))
```

## Arrays
```java
int[] ints = new int[10];
Integer[] ints2 = new Integer[10];
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
```
> 固定長度 可放原生類型
> 透過java.util.Arrays工具類延伸操作

## ArrayList
> 元素為連續排列 但能動態調整大小(會預留空間) 從刪除或插入會造成比較大的開銷
> get/set能透過索引算出 `快`
> add/remove需要移動整個右邊元素 `慢`

## LinkedList
> 以雙向鏈(node)實現List
> get/set需透過node遍歷 `慢`
> add/remove直接調整左右節點即可 `快` (節點不在最前最後時要先遍歷找到節點)

Iterator (迭代器)
======
用於遍歷Set與List元素 類似一個for loop (Map的遍歷透過其EntrySet)
- ListIterator : 可雙向
- fail-fast : 操作集合會累加modCount以示集合出現更動 若迭代時出現更動會拋出異常java.util.ConcurrentModificationException 算是一種偵錯機制
- fail-safe : 迭代時複製一分參考清單 該清單無人能更動 自然就能確保迭代期間是完整的 但期間若真有異動也無法即時反應

Stream (流)
======
類似以loop方式每次推送一個元素到function中操作 透過各式操作符一層一層完成以往for loop的工作

> parallelStream : 以並行(切割任務)方式處理流

同步
======

```java
Map map = Collections.synchronizedMap(new HashMap());
```

JDK集合實現
======

```java
List

//Single-threaded
ArrayList – 基於數組的泛型
LinkedList – 請勿使用
Vector – 已棄用

//Concurrent
CopyOnWriteArrayList – 極少更新，常用於遍歷
```

```java
Maps

//Single-threaded
HashMap – 泛型映射
EnumMap – 枚舉鍵
Hashtable – 已棄用
IdentityHashMap – 用== 對比的鍵
LinkedHashMap – 保持插入次序
TreeMap – 有序鍵
WeakHashMap – 有益於緩存

//Concurrent
ConcurrentHashMap – 泛型並發映射
ConcurrentSkipListMap – 有序並發映射
```

```java
Sets

//Single-threaded
HashSet – 泛型集合
EnumSet – 枚舉集合
BitSet – bits/dense 整型集合
LinkedHashSet – 保持插入次序
TreeSet – 有序集合

//Concurrent
ConcurrentSkipListSet – 有序並發集合
CopyOnWriteArraySet – 極少更新，常用於遍歷
```


```java
Queues / deques (隊列/雙隊列)

//Single-threaded
ArrayDeque – 基於數組的泛型
Stack – 已棄用
PriorityQueue – 有序檢索操作

//Concurrent
ArrayBlockingQueue – 有界阻塞隊列
ConcurrentLinkedDeque / ConcurrentLinkedQueue – 無界鍊式隊列(CAS)
DelayQueue – 由延遲元素構成的隊列
LinkedBlockingDeque / LinkedBlockingQueue – 可選有界鍊式隊列(locks)
LinkedTransferQueue – 可傳輸元素且不進行存儲
PriorityBlockingQueue – 並發PriorityQueue
SynchronousQueue – Queue 接口的交換器
```

