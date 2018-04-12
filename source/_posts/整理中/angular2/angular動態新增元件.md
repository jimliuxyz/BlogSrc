---
title: __
---

[參考](http://blog.rangle.io/dynamically-creating-components-with-angular-2/)

```js

//tmeplate會變成HTML註解(comment)元素,元件會插入成為其next sibling(不是child)
<template #placehere></template>

@ViewChild('placehere',{read:ViewContainerRef}) tplvcf: ViewContainerRef;

//用ComponentFactoryResolver建立factory
let factory = this.cfr.resolveComponentFactory(MyComponent);

//建立元件並安插
let compref = this.tplvcf.createComponent(factory, this.tplvcf.length, this.tplvcf.injector);

```

ng元件的nativeElement的 Dom樹狀結構 與 ng元件的依賴結構 沒有一定的關聯,只是設計習慣上會使兩者結構相似,但實際上可以完全不相干,尤其是動態新增的元件. 也就是說建立元件時的依賴可以來自任何能取得的Injector,安插在任何能取得的ViewContainerRef.

```js
//分離 建立元件 與 安插

//建立元件
let compref = factory.create(this.tplvcf.injector);
//安插
this.tplvcf.insert(compref.hostView);
```

### 建立Provider

被依賴的對象須建立一個provider物件才能傳遞給injector. 有四種類型

```js
//useClass
{ provide: ApiService, useClass: ApiService }
//或直接用 ApiService, ng也能識別

//useValue
{ provide: 'MyApiService', useValue: myService }

//useExisting
{ provide: 'ApiServiceAlias', useExisting: ApiService }

//useFactory
export function configFactory(config: AppConfig) {
  return () => config.load();
}
{ provide: APP_INITIALIZER, useFactory: configFactory, 
        deps: [AppConfig], multi: true }

```
假設要provide一個服務. provide就像是服務的名稱, useXXX則像是提供的實際內容物.

### 建立Injector (加入其他provider)
```js
let resolvedProvider = 
        ReflectiveInjector.resolve([{ provide: 'myService', useValue: myService }]);    
let injector = 
        ReflectiveInjector.fromResolvedProviders(resolvedProvider, this.vcf.parentInjector);
```
