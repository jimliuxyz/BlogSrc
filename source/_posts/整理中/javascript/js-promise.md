---
title: __
---

### 運作1 callback
```js
var promise = new Promise((resolve, reject) => {
  if (ready)
    resolve('ok');
  else
    reject('err');
})

promise.then(cbOk).catch(cbErr);

function cbOk(data){
}
function cbErr(err){
}

//resolve -> then's callback
//reject -> catch's callback
//設計promise時最後一定要呼叫resolve或reject
//無論resolve/reject被呼叫幾次,僅有第一次會產生callback

```

### 運作2 狀態
```js
var promise = new Promise((resolve, reject) => {
  setTimeout(function() {
    resolve(999);
  }, 1000);
})
    
promise.then(data => {
  console.log('a', data)
  //於1000ms後印出999
})
    
setTimeout(_ => {
  promise.then(data => {
  console.log('b', data)
  //於3000ms後印出999
})}, 3000)

//1. promise在new後就已經開始異步執行 (這算是一個缺點)
//2. then/catch僅是依其既有狀態呼叫callback
//說明同一prmise的狀態是能夠"續用"的.
```
### 運作3 await
```js

async function func(){
  var value;
  setInterval(_ => { console.log(value) }, 1000)

  value = new Promise((resolve, reject) => {         // case A
  value = await new Promise((resolve, reject) => {   // case B
    setTimeout(function() {
      resolve(999);
    }, 3000);
  })
}

//case A : 始終只會印出promise物件
//case B : 會從undefined變到999,但code需寫到async中,也就是處於異步

```

### 運作4 thenable
```js
async function func(){
  var value = await new Promise((resolve, reject) => {   // case B
    setTimeout(function() {
      resolve(999);
    }, 3000);
  })
  .then(data=>{return data+1;})
  
  console.log(value); //1000
}
//then's callback可以接續另一個promise或回傳值
```

### 執行多個關聯的異步操作

將多個promise放置到一個async或promise異步執行,其中再用await連結成同步執行.
也許中間也有一些能夠單獨操作的promise,但如果loading不重放一起也無仿,畢竟要做到極致的異步分工在開發時間的開銷也是很吃重的,況且也會增加程式碼的複雜度,造成後續修改的難度.

```js
async func(){
    let a = await promise....
    let b = await promise(a)...
    let c = await promise...
}


```

### 多個異步並發執行
Promise.all

### 多個異步搶快執行
Promise.race
