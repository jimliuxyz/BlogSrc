---
title: __
---

## 從template取得元件

```js
//.html
<ion-content #myid></ion-content>
<div #mydiv></div>


//.ts
@ViewChild('myid') mycom:Content
@ViewChild(Content) mycom:Content //by component class name, and return first one of page
@ViewChild(Content) set content(c:Content){...} //check with setter

@ViewChildren(Pane) panes: QueryList<Pane>;
@ViewChild('mydiv') mydiv: ElementRef ;


this.mycom.setElementStyle("border", '1px solid red');
this.mydiv.nativeElement;

```

- @ViewChild可使ng**動態**改變reference(參考*ngIf,動態選到第一個)

## bind

- () 資料從dom(html)流向module(ts), 綁定'事件'發生的處理方式
- [] 資料從module(ts)流向dom(html), 綁定'變數'改變直接導向dom元件
- [(ngModel)] 僅有ngModel一類的directive能用(還沒看過別的)

```js

//.html
<input [(ngModel)]="myvar">
<input (ngModelChange)="myvar=$event" [ngModel]="myvar">
<input (input)="myvar=$event.target.value" [value]="myvar">
//以上三種寫法都能達到同等功能

<input #inp (keyup)="myvar=inp.value">
<input (keyup.enter)="sendMsg()">

//.ts
myvar:string;

```

## ngModel

- NgModel一種雙向綁定 但只限於表單控制項 (FormControl)
- NgModel 主要用來建立一個 表單控制項 (FormControl) 實體
- (ngModel)是沒有意義的

```js
<input ngModel #mUsername="ngModel">
<input [ngModel]="username" #mUsername="ngModel">
<input [(ngModel)]="username" #mUsername="ngModel">

```

## form

相關directive依階層排列如下

- ngForm
- ngModelGroup
- ngModel : 通常html tag上要有name屬性,方便ts編寫時依name檢查

## 元件中加入ngModel功能

[ionic3-chat](https://github.com/HsuanXyz/ionic3-chat/blob/master/src/components/emoji-picker/emoji-picker.ts)

- 綁定A時另一端(ngModel)會產生一個A'的變數,兩個變數靠ng居中做髒檢查才能提供這樣的功能. (用起來像是同一個變數,實際上是兩個,可以用getter/setter與debugger做時間差的驗證)

```js

export const EMOJI_PICKER_VALUE_ACCESSOR: any = {
  provide: NG_VALUE_ACCESSOR,
  useExisting: forwardRef(() => EmojiPickerComponent),
  multi: true
};
export class EmojiPickerComponent implements ControlValueAccessor{
    //obj is the var you need to bind (from ngModel)
    writeValue(obj: any): void;
    //call this fn when data(obj) changed
    registerOnChange(fn: any): void;
    registerOnTouched(fn: any): void;
}

```

## 追蹤函數呼叫
```js
function funclog(target: any, key: string, descriptor:any) {
  var orifunc = descriptor.value;
  var cnt = 0;
  descriptor.value = function (...args) {
    console.log('>>>'+key + " x " + (++cnt))
    return orifunc.call(this,...args);
  }
}

@funclog
afunc(){
}
```

