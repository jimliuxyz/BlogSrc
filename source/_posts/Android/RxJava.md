---
title: RxJava
date: 2018-04-06 11:49:48
tags: 
    - Android
    - ReactiveX
---


## stream的類型
- cold : 如同隨選視訊 流是固定的 訂閱才送 每次送都是完整的 不論是否送過給別人
- hot : 如同直播 流是不斷送出的 無法減速的 每次訂閱都只能從當下開始 沒有終點的

## 物件解釋

```java
//基於Obserable建立一個可訂閱對象
Obserable.create(
    new OnSubscribe<String>(){
        @Override
        public void subscribe(Emitter<String> e){
            //發送事件
            e.onNext("new event!");
            e.onComplete();
        }
    })
    .subscribe( //訂閱並設定接收的對象 參數列:onNext, onComplete, onError
        new Consumer<String>() {
            @Override
            public void accept( String s) {
                System.out.println(s);
            }
        },
        new Consumer<Throwable>() {
            @Override
            public void accept(Throwable throwable) {
                throwable.printStackTrace();
            }
        });

```
[ref](https://www.jianshu.com/p/45309538ad94)

- Obserable : 可觀察對象 除此還有多種不同類型的可觀察對象可以create
- OnSubscribe : 可訂閱對象 負責發送產生事件
- Emitter : 相當程度代表一個訂戶(多少有連結關係)
- Observer : 觀察者 即訂戶 接收事件
- Consumer : 一個通用的介面 帶有一個accept方法 該方法被叫用的意義與時機由外界定義
- BiConsumer : 同Consumer 但accept能傳如兩個參數
- upstream / downstream : 即兩個observable的相對應關係 donw=up.map(...)

## 大致流程(一種意象上的形容 幫助記憶)
- 最末端的observer開始o.subscribe(observer) 一路向上訂閱
- 操作符也如同一個observer+observable (向上進行訂閱 向下提供可觀察對象)
- 一直訂閱到最頂端的observable (到頂端前,中間的操作符也可能發射值)
- observer相當於會被丟到OnSubscribe.subscribe()直接被呼叫onNext/onError/onCompleted
- 一個observable被重複訂閱也是相同的流程 但不見得會發射一樣的值 要視observable設計與規劃而定 例如cold與hot的差別

## Observables
[RxJava搭配Room](http://www.jcodecraeer.com/a/anzhuokaifa/androidkaifa/2017/0726/8268.html)

- observable
- flowable : 有backpresure策略 可控制流速
- single : 只發1次(onSuccess)或0次(onError)事件 沒有其他選項
- completable : 沒有事件 只有成功與否 只有onComplete與onError 常用.andThen(...)操作符再轉出其他obserable
- maybe : single+completable

> 一次性使用 : single / maybe

> 僅關注成敗 : completaion

## emmiter interface
- onNext(T t) 多事件型會用這個關鍵字
- onSuccess(T t) 單事件型 相對應的是onError(Throwable t)

## observer interface
observer與emmiter的介面有點像是mapping 呼叫emmiter的onNext近似於observer的onNext被呼叫

- onError後 onComplete應該就不會被呼叫了

## Subject
[ref](https://www.jianshu.com/p/1257c8ba7c0c)
> subject同時擁有observable與observer的能力. 做為observer可以直接透過subject實體餵入事件onNext("event") 做為observable能讓外部訂閱 算是一種整合應用

> 做為observer很單純 但做為observable則有很多變化

- PublishSubject 沒有任何特殊機制
- BehaviorSubject 訂閱時會發射預設值或最後一值 像是一個狀態變數一樣 任何時候都有值
- AsyncSubject 只在onCompleted才發射值(最後一個)
- ReplaySubject 任何時間訂閱都從頭再發射一次

## 操作符
> 操作符回傳的都是Observable 但向操作符提供的function僅是完善操作符的一小部分 func的in/out與操作符的out是不相干的

> 操作符的應用 應考慮流的屬性(hot/cold)

## 操作符 - 結合
#### concat
一個流結束 才切到下一個流

#### marge
多個流同時穿插 當一個流onError就全部結束

## 操作符 - 建立
#### create
> 自訂OnSubscribe 自己提供值來發射

#### from
> 用Iterable的內容值發射

#### just
> 從參數列給值來發射

#### defer
> 產生新的observable (每次訂閱就會重新產生)

#### Interval
> 值從0開始 每固定時間發射一次

#### timer
> 在一段時間之後發射0 之後就completed

#### range
> 分別發射一個區間的值

#### repeat
> 將收到的值重複發射N次

## 操作符 - Transforming 將值變形
#### buffer(count)
> 累積count個值 轉成array發射 

#### buffer(count, skip)
> 累積skip個值 只取count個 轉成array發射 

#### window(count/time)
> 同buffer 但包裝成Observable 可用時間做buffer

#### map
> 將值個別map成新的值 ex:1 => "1"

#### flatMap
> 將值個別map成以Observable產生的值 ex:1 => Observable.just("1")

#### flatMapIterable
> 將值個別map成以Iterable產生的值 ex:1 => listOf("1")

#### map說明
> 以上三個map操作符輸出的都是Observable\<T> 僅只是用不同的方式建立內容

#### scan
> 類似map的用法 但多一個acc的累加參數 (如若有設初始值,在subscribe時會先送初始值,無論上游是否有值)

#### groupBy
> 依值建立GroupedObservable 返回Observable\<GroupedObservable\<K,T>>

#### cast
> 轉型

## 操作符 - Filtering

#### debounce
> 抗反彈 收到值後需N時間後無新值才會發射 否則用新值且N重算

#### throttleWithTimeout (同throttleLast?)
> 同debounce 但N不會重算

#### throttleFirst
> 同debounce 但N不會重算 且取的是最先發生的值

#### sample
> 固定時間內取最新的一筆 時間的起點與訂閱時間有關 與資料發生時間無關

#### distinct
> 僅發射不同的 從未出現過的值

#### distinctUntilChanged
> 僅發射與前一個值不同的值

#### elementAt
> 僅發射第N個值(接收到的第N個)

#### filter
> 僅發射函數返回為true的值

#### last / first
> 僅發射函數返回為true的第一或最後一個值 發射完即completed

#### take
> 僅發射固定筆數 發射完即completed

#### skip
> 忽略前面N筆

#### blocking...
> 將上游阻塞 待下游... 用途尚未確認

## 操作符 - Combining

#### Observable.combineLatest(O1,O2,combiner)
> 當O1或O2有值時透過combiner函數決定發射的值 (傳入O1,O2的最新值) (多形用法還多)

#### join(O2,T1,T2,combiner)
> 將現有Observable(O1)與O2結合 T1/T2分別表示O1/O2有效的時間 兩者都在有效時間內時會呼叫combiner進行發射

#### joinGroup
> ...

#### Observable.merge
> 混合兩個Observable

#### mergeWith
> 同merge 但沒試出結果

#### startWith
> 開頭時插入值

#### switch
> 當一個O發射多個O時 切換到最新發射的O

#### Observable.zip(O1,O2,O3,zipper)
> ... 將O1,O2,O3的值依序合併成新值

## 操作符 - 異常處理
#### onErrorReturn
> 捕捉onError異常 並補一個值 之後onCompleted

#### onErrorResume
> 同onErrorReturn 並補一個Observable繼續運作

#### OnExceptionResumeNext
> 針對Exception處理

#### retry / retryWhen
> onError時重試 retryWhen要注意forever loop問題


## 操作符 - Utility

#### delay
> 延遲值的發射

#### delaySubscription
> 延遲訂閱的時間

#### doOnEach/doOnNext/doOnSubscribe/doOnUnsubscribe/doOnError/doOnCompleted/doOnTerminate/finallyDo
> 監聽事件 直接回傳上游Observable

#### materialize
> 將事件與值封裝為Notification類型

#### dematerialize
> 將Notification解回事件與值

#### [ subscribeOn / observeOn ]
> 將個訂閱過程拆分"訂閱時期"與"觀察(遞值)時期" 訂閱時是由下往上(code的順序) 觀察時期是由上往下 

#### subscribeOn
> 在訂閱時期指定thread (訂閱時期比較短 相應的函數通常只執行一次)

#### observeOn
> 在觀察(遞值)時期指定thread (觀察(遞值)時期比較長 相應的函數通常反覆執行)

#### timeInterval
> 發射值與前一個值之間的時間差

#### timeStamp
> 發射值發生時的時間戳

#### timeout
> 逾時未出現值則發出onError 或發出endWith+onCompleted

#### using
> 有點像是建立一個與該Observable伴隨生命的物件

## 操作符 - Conditional and Boolean

#### all(func)
> 根據func的回傳 如果至onCompleted都沒有false 就發射一個true 然後onCompleted

#### amb(O1,O2...)
> 讓O1,O2...競賽 以最先有值或事件的O做為發射的來源

#### contains(func)
> onCompleted前func回true則發射true 否則發射false 然後onCompleted

#### isEmpty
> (true or flase) + onCompleted

#### defaultIfEmpty
> empty時發射default+onCompleted

#### sequenceEqual(O1,O2)
> 用來判斷O1,O2的發射值與順序是否一模一樣

#### skipUntil(O2)
> 直到O1出現O2的pattern前都skip 

#### skipWhile(func)
> 直到func回傳false前的值都skip

#### takeUntil / takeWhile
> ... 如上類似

## 操作符 - 合計Aggregate

#### contact
> 合併兩個O 但第一個onCompleted才能切第二個

#### count
> 在O結束時 發射值的數量然後onCompleted

#### reduce
> 壘算 直到O結束才發射 然後onCompleted (類似scan 只是scan是每次壘算都發射)

#### collect(init,collector)
> 將值收集到ArrayList之類的物件 在O結束時才發射 然後onCompleted

## 操作符 - Connectable

#### publish
> 將O轉成需要connect才發射的型態(Connectable) 主要用意在控制多個observer時的發射時機

#### connect
> 當connect時該Connectable即已經開始訂閱上游

#### refCount
> 將Connectable轉回普通的O

#### replay
> 轉為一個Connectable 並保留數個值或一段時間的值做新訂閱者的startWith (字面很像重播 但實際比較像buffer的效果)

## 操作符 - 自訂操作符
[ref](http://mushuichuan.com/2016/02/05/rxjava-operator-10/)

#### compose (自定義/加工observable)
> Transformer 加工上游observable 再返回另一個observable 可以當成一個聚合的操作符 執行一個compose裡面藏了很多操作符

#### lift (自定義observer)
> 能插入一個Operator/Subscriber

