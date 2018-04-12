---
title: __
---

## 髒檢測觸發
[Angular 2 Change Detection-1](https://segmentfault.com/a/1190000008747225)

1. Events：click, mouseover, keyup ...
2. Timers：setInterval、setTimeout
3. XHRs：Ajax(GET、POST ...)

- zone.js : 它是用於攔截和跟蹤異步工作
- Monkey-patched : 覆寫預設method

## Change Detection提高效率
[Angular 2 Change Detection-2](https://segmentfault.com/a/1190000008754052)

[Change Detection](https://blog.kevinyang.net/2017/01/23/angular2-change-detection/)

[markForCheck](https://blog.kevinyang.net/2017/08/09/angular-changedetector-markforcheck/)

1. input需要改變時採immuteable策略,用Object.assign({}, person, {name: 'new name'});複製物件再整個assign到input,避免ng做深層的髒檢測
2. 改為手動策略changeDetection: ChangeDetectionStrategy.OnPush,改用ChangeDetectorRef.markForCheck()的方式推動髒檢測

## ngZone




