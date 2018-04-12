---
title: __
---

### template動態產生元件

[參考](https://blog.kevinyang.net/2016/11/12/ng2-template/)

```js
import { Component, ComponentFactoryResolver, ViewChild, ViewContainerRef } from '@angular/core';

@Component({
  selector: 'app-root',
  template: `
  <template #nametag let-y>
    <div>Hello {{ y.name }}</div>
  </template>

  <div [ngTemplateOutlet]="nametag" [ngOutletContext]="myContext"></div>
  <div [ngTemplateOutlet]="nametag" [ngOutletContext]="myContext2"></div>
`
})
export class AppComponent {
  name = 'kevin';
  myContext = { '$implicit': {name: 'kevin'}};
  myContext2 = { '$implicit': {name: 'Jeff'}};
}
```

- ngTemplateOutlet指向TemplateRef
- ngOutletContext指向dataContext
- $implicit隱含變數(以字串形式建立,ng會將其轉為別名)
- let-$implicit的別名

### template動態產生元件

動態建立元件 並加入template (類似*ngFor/\*ngIF)

```js
import { Component, ComponentFactoryResolver, ViewChild, ViewContainerRef } from '@angular/core';

//MyDynamicComponent因為是動態產生的,必須加到ngModule meta的entryComponents
@Component({
  selector: 'my-dynamic-component',
  template: `<div>Hello world</div>`
})
export class MyDynamicComponent{  
}

@Component({
  selector: 'app-root',  
  template: `
  <div>
    <template #target></template>
    <h1>{{title}}</h1>
  </div>
` 
})
export class AppComponent {
  @ViewChild('target', {read: ViewContainerRef}) target: ViewContainerRef;
  title = 'app works!';
  constructor(private cfr: ComponentFactoryResolver){   
  }
  ngAfterViewInit(){
     //MyDynamicComponent須加到module的entryComponents
     let myFactory = this.cfr.resolveComponentFactory(MyDynamicComponent);
     let compRef = this.target.createComponent(myFactory);
  }
}
```

### 自訂Directive

```js
import {
  Component, Directive, Input, TemplateRef,
  ViewContainerRef, EmbeddedViewRef, ChangeDetectorRef, ChangeDetectionStrategy
} from '@angular/core';
import { Observable } from 'rxjs/Rx';

@Directive({
  selector: '[rxContext][rxContextOn]'
})
export class RxContext {
  @Input() rxContextOn: Observable<any>;
  _viewRef: EmbeddedViewRef<any>;
  //TemplateRef:該directive所在的tag即為template (directive須加*才會inject)
  //ViewContainerRef:其directive的tag容器所處的位置
  constructor(private templateRef: TemplateRef<any>, private vcr: ViewContainerRef) {    
  }
  ngOnInit() {
    // console.log(this.rxContextOn);
    this.rxContextOn.subscribe(state => {
      if (!this._viewRef) {
        //在容器上坎入新的view
        this._viewRef = this.vcr.createEmbeddedView(this.templateRef, { '$implicit': state });
      }
      this._viewRef.context.$implicit = state;      
    });
  }
}

@Component({
  selector: 'app-root',  
  template: `        
      <div *rxContext="let user on userStream">
        <h2>{{ user.name }}</h2>
      </div>`
})
export class AppComponent {
  userStream = Observable.of({
    name: 'kevin',
    age: 35
  }).concat(Observable.timer(3000).mapTo({ name: 'Jeff', age: 30 }));
}
```
template拆解

```html
<template rxContext let-user [rxContextOn]="userStream">
   <div>
       <h2>{{ user.name }}</h2>
   </div>
<template>
```

