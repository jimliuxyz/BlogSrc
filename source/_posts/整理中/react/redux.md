---
title: __
---


### 概念

redux基本上是針對狀態讀寫(不是針對變數)的一種設計思想,把散落的狀態讀寫集中到一個稱為'store‘的模組上.

### 制定狀態結構
```js
interface SpeedState {
  speed: number;
}

INI_STATE: SpeedState = {
  speed: 0
};
```
### 怎麼寫入狀態？

狀態的寫入需要透過發送'action',其實就是一個{type,payload}的物件,做為一個命令傳送給store.

```js
store.dispath({
    type:SET_SPEED,
    payload:{
        speed:1
    }
});
```

### 誰處理action

處理的函數稱做reducer,這個名稱來自於Array的reduce函數,也提供類似的行為new=f(old,act)

reducer須符合pure function的設計,意即同樣輸入必須得到同樣輸出,這是設計理念,涉及狀態回朔的可能性,與何者該被定義為'狀態'有關.

```js
function speedReducer(state: SpeedState=INI_STATE, action: Action): SpeedState {
  const {type, payload} = action;

  switch (type) {
    case SET_SPEED:
      //依照action.type產生新的狀態(以不修改原state的方式使其舊state能保留)
      return {...state, speed: payload.speed};

    default:
      return state;
  }
```

### 建立store

```js
import {StoreModule} from '@ngrx/store';
...
@NgModule({
    imports: [
      StoreModule.provideStore(speedReducer),
    ]
...
```

### 怎麼讀？

```js
mynum: Observable<number>
mynum = store.select('num');    //回傳observable
```

```html
<p>num: {{ mynum | async }}</p>
//或直接subscribe observable(mynum)進行更多處理
```

### 結論

redux架構的使用很容易導致over design,用ng service + RxJs會比較精簡些

[http://oomusou.io/angular/angular-ngrx/](http://oomusou.io/angular/angular-ngrx/)

[https://www.youtube.com/watch?v=hJtUdXqPLDc](https://www.youtube.com/watch?v=hJtUdXqPLDc)
