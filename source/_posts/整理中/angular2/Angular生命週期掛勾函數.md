---
title: __
---


```js

import {
  OnChanges,SimpleChanges,
  OnInit,
  DoCheck,
    AfterContentChecked,
    AfterContentInit,
    AfterViewChecked,
    AfterViewInit,
  OnDestroy
} from '@angular/core';

```

- Init/Destory的動作僅會執行一次,之後的髒檢查不會再被呼叫,屬於生命週期的部分.其他Check的部分會隨髒檢查循環執行,與'生命'無關.

- AfterViewInit : View已完成初次初始化,表示已能取得View上的元件參考,也可以再執行變更View相關的動作,

- AfterViewChecked : 表示已經做完View的髒檢查,若在此又執行變更View相關的動作,會造成顯示後立刻又進入DoCheck,變成無窮循環.

### 運用時機
動態建立view時需善用onInit與afterViewInit.

- onInit時雖沒有任何view ref,但能初始化一些變數,改變view的結構(ngIf/ngFor...)
- afterViewInit是最後改變'第一畫面'的機會,該取得的view ref都該趁onInit時製造出來.


