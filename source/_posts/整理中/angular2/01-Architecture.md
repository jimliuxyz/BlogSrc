---
title: __
---

程式進入點通常為main.ts

(這裡的module與js中import/export的module是不同的層級的觀點,一個是js檔案結構,一個是ng的應用結構)

function call前有加@,表示前置函數,其後接的class宣告將一起被帶入該函數,ng用這樣的方式把custom classes與ng系統結合

---

1.modules

@NgModule({METADATA})
export class AppModule{}

基本上都會有一個root module(即AppModule),視需要在加入其他feature module
root module : 一定會import'BrowserModule',因為與網頁瀏覽(view/route)相關
feature module : 一定會import'CommonModule'

[METADATA]
declarations: 宣告module裡需要用到的'view classes',包含components,directives,pipe
eports ? 將自己declarations中的feature module開放
import : 引入feature module供component使用(自動注入?)
providers : 宣告共用的DI services,供其他部件
bootstrap : 指定root component,相當於指定view的初始畫面. 只有root module需要指定bootstrap
directives : 使用<subcom [props]="value">將value傳給subcom的props時,需將subcomp列為direvtives

---

2.components

@Component({METADATA})
export class HeroListComponent{}

類似MVC中的control角色,負責UI流程

[METADATA]
moduleId: module.id,指定後Url的部分可以設定為相對路徑
selector: 連接該元件的HTML tag名稱
providers: 宣告元件私有的DI services
templateUrl: 該元件的template位址
style/styleUrls:css style,其style不會擴散到component外,也不會到sub component,完全只在自己的template中套用
inputs: 用法同@input

---

3.templates

即HTML模板

Angular's template syntax
https://angular.io/guide/template-syntax

---

4.data binding

bind的用途1.顯示資料 2.傳遞資料(透過props)
除了bind字串數字外,也可以bind物件

(->為bind to,資料流向之意)

interpolation (component value -> DOM)
<li>{{hero.name}}</li>

property binding (component value -> DOM)
<hero-detail [hero]="selectedHero"></hero-detail>

event binding (DOM event -> component)
<li (click)="selectHero(hero)"></li>

Two-way data binding (component value <-> DOM)
<input [(ngModel)]="hero.name">

-

parent.value -> subcom.props
<subcom [props]="value"></subcom>
<subcom props={{value}}></subcom>

parent.value <-> subcom.props
<subcom [(props)]="value"></subcom>

parent.value <- subcom.props
???


---

5.directives

諸如*ngFor與*ngIf甚至做two way binding的[(ngModel)]都是directive
連component都是一個繼承了template導向的directive

import { Directive, ElementRef, Renderer } from '@angular/core';
@Directive({ selector: '[myHidden]' })
export class HiddenDirective {
    constructor(el: ElementRef, renderer: Renderer) {
       renderer.setElementStyle(el.nativeElement, 'display', 'none');
    }
}

一個Html tag中可能有多個directive,每個各自都會被執行
若有兩個directive的selector的名字是一樣時,就看該元件declarations中加入directive的順序,依序執行

---

6.Services

任何物件都可以成為Service,不需要extends或implements任何類別
可以當作是一個MVC中module的概念,或任何的運用
主要是service加到METADATA中的provider後能提供依賴注入(DI)

---

7.dependency injection
依賴注入,簡單說就是一個分工的概念,將模組依某特性劃分開,以利重複使用與管理.
在程式碼上的實踐就是將其依賴的模組從參數列傳入.

宣告方式:
METADATA...providers:[HeroService]

注入方式:
constructor(private service: HeroService) { }

module providers:所有component共用一個實體的service
component providers:每個實體的component都有自己一個實體的service

---


