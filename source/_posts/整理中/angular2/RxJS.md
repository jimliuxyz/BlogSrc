---
title: __
---

http://blog.techbridge.cc/2016/05/28/reactive-programming-intro-by-rxjs/
https://segmentfault.com/a/1190000005051034

---

Observable算是一種設計邏輯,一般函數呼叫的運作都是同步的,偶爾會希望它是異步的,
所以會用setTimeout的方式進行異步,但setTimeout在情境意義上比較缺乏,Observable就像加入情境意義的setTimeout

通常的設計都有'提供服務者'與'被服務者',就像server與client的關係,溝通上可以用push與pull來區別
pull(拉取) client主動 向 server取得資料
push(推送) server主動 向 client推送資料

其中pull又稱作'輪詢',即定時詢問之意,其資料的即時性掌握在client的主動程度;
push則類似youtube的頻道訂閱(subscribe),若有訂閱,在頻道更新時會由server主動推送通知

兩者各有所長,例如pull適合應用在'取樣'大量事件,或有些較低階的語言不易實行push
push最大的優點就是即時,又有效率,但也有些情境讓push很為難,像是上述的大量事件,要每個事件都
即時傳達是不可能,也可能造成server超過負荷

js最常用到的push方式就是button event,透過addListener向event emitter註冊一個event listener
觀念轉到RxJS上,event emitter就是Observable,event listener就是Observer

---


Observable
基本上透過Rx.Observable.create建立,或從第三方應用中取得






