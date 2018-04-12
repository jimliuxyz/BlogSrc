---
title: __
---

## plugin

autoimport

## .ts區分script與module

與轉譯編譯有關

- script : 變數宣告會直接到global(window)下,或說不是被import而執行的
 - angulre.cli.json的scripts會以script執行
- module : 有用到export的script就會變成一個module,變數宣告會在某個namespace下
 - 此時定義window下的global變數要用`declare global`

**簡單說module是存在於global(window)內的另一個scope**

vscode在type的hint方面整合很不錯,幾乎可以預先分析所有的狀況

## import

若import的不是ts則沒有types可以參考除錯.

```js
// your.ts
import * as objectHash from 'object-hash';

console.log(objectHash.MD5('str'))
```

- 同一個module多次import也只會被執行一次(export出來的物件是共享的)

## 什麼適合放到angular.cli.json的scripts
當js/ts被放到angulare.cli.json的scripts下其實就已經相當於html的< script src=...>的global執行情境(相對於import module),已經放在scripts中的就不需要再被import了,若再import會變成在另一個namespace再建立一次.

- 與module無關的,自己可以運作的,例如bootstrap.js
- 有@types像JQuery,放進去後就不用再import了
- 沒有@types,直接把type定義成any (像是object-hash)

```js
//angular.cli.json
scripts : "...object-hash.js"

// yourmodule.ts, no need to import * from 'object-hash'
// but you got to know the global name of it, is 'objectHash'
// you can also check it by console.dir(window)
// decleare沒有執行順序(僅供編譯器與lint識別)
declare global{ //在module裡定義script(global)的變數
  let objectHash: any;
}
console.log(objectHash('str'))
```

```js
//或將定義寫到d.ts
//typings.d.ts
declare let objectHash: any; //不加global,因為是script執行不是module
```
- 3rd party extension (透過import方式則無法在前期執行完畢)

```js
//angular.cli.json
scripts : "... extension.ts"

//extension.ts
interface JQuery {
  removeClassRegex(regex: RegExp): JQuery;
}
$.fn.removeClassRegex = function(regex) {
  return $(this).removeClass(function(index, classes) {
    return classes.split(/\s+/).filter(function (c) {
    console.log(c + " ? " + regex.test(c))
  return regex.test(c);
  }).join(' ');
  });
};
```

## extend 3rd party function

```js

//entry.ts (import any object of extends.ts to make compiler link it)
import { Empty } from './mods/pure/global'

//extends.ts (module)
declare global {
  interface JQuery {
    removeClassRegex(regex: RegExp): JQuery;
  }
}

$.fn.removeClassRegex = function(regex) {
  return $(this).removeClass(function(index, classes) {
    return classes.split(/\s+/).filter(function (c) {
    console.log(c + " ? " + regex.test(c))
  return regex.test(c);
  }).join(' ');
  });
};

export class Empty{
}

```


## 取得html元件

```js

template:`<div #mychild></div>`;

import { ViewChild } from '@angular/core';

@ViewChild('mychild') elem; 

this.elem.nativeElement;

```


