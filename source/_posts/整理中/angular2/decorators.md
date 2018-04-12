---
title: __
---


@input
mytype:number=1;
component中變數加上@input即可對應HTML tag上的props

可以用別名對應外部的props name
@input('propsColor') color:string;

---

@output
myclick = new EventEmitter<number>();
...myclick.emit(value)
component中變數加上@input即可對應HTML tag上的事件

<mycom (myclick)="myclickhandle($event)">
</mycom>

其中$event是ng的保留字,必須這樣寫
在JS框架中很中很多對應都是字串解析對應到程式碼宣告,對應關係從字面會很難理解,需要透過手動試驗才比較好搞懂

---

***自訂two way binding要做兩件事

<mycom [(myvalue)]="parentvar"><mycom>

@Input() myvalue: number;
@Output() myvalueChange: EventEmitter<number> = new EventEmitter<number>();

1.output前綴字為input+Change
2.必須要自己發emit
3.如果只用[myvalue]的方式binding,必須再寫(myvalueChange)="..."自己處理

---

@ContentChild與@ViewChild  (@ContentChildren / @ViewChildren)
兩者同為取得child component,但取得的時機與component所處的

@ContentChild(ChildComponent) child:ChildComponent;
@ContentChildren(ChildComponent) childs:QueryList<ChildComponent>;

1.
ContentChild取得的child是透過<ng-content>引入的child
ViewChild取得的是自己template裡加上的child

2.
ContentChild是在ngAfterContentInit時取得
ViewChild是在ngAfterViewInit時取得

這也是因為ContentChild的建立比ViewChild的建立早

---

