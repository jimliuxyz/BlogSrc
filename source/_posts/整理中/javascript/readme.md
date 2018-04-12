---
title: __
---

資料型態

string
number
boolean
null and undefined
object
symbol (new to ES6)

---

物件建立
一個物件一定會有__proto__,但不一定有prototype (function一定有prototype)

__proto__:是物件實質的繼承鏈
prototype:僅用來規劃繼承內容(所以function之所以稱構造函數,一定要有prototype)

var Car = function(){this.speed=100}
當mycar=new Car()時,mycar的__proto__會被指向Car.prototype,當作mycar的繼承鏈

---

邏輯運算子的延伸

x=A||B||C
依序檢查,第一個為true的即指定給x,若全為false則指定最後一個

x=A&&B&&C
依序檢查,第一個為false的即指定給x,若全為true則指定最後一個

---

Hoisting(提升)

指用'var'宣告的變數將被拆為declaration與assignment,
declaration的部分將被提升到scope的上方,其值會先處於undefined

'var'屬於function scope,將被'let'的block scope取代

---

Closure
閉包. 若將function視為一個物件,若呼叫funcA又回傳funcB,此時funcA會自動產生一個物件實體,
只要透過funcB就能持續存取這個實體的props.
First-class function就是形容有這樣特性的程式語言,將function視為物件(頭等公民)

function funcA()
{
  let c=0;
  return function() {return c++;}
}

let funcB = funcA();
console.log(funcB(),funcB(),funcB()); //0 1 2

---

who is this
函數中的'this'指向誰

function()宣告 : this指向呼叫該function的object
()->{}宣告 : this指向建立arrow function的對象(即該函數的外層),右邊不加{}即直接返回值,列如:()=>3

---

bind this
綁定function的this對象

以function()宣告為例,this會指向呼叫該function的object
例如myobj.func(),func中的this就會指向myobj
但let myfunc=myobj.func後在最外層執行myfunc(),此時this會指向window(root object)
造成func中的this沒有固定的對象

若要綁定this到固定的對象可以用
let myfunc=x.func.bind(myobj);
myfunc();
或
myfunc.call(myobj, arg1, arg2);
myfunc.apply(myobj, [arg1, arg2])

PS: bind產生出來的func與原func都仍在同一個closure object裡,只是this指向不同

---

[]除用在array外,還用於computed property names或key-name

myobj.var1
myobj["var"+1]

key-name的部分與Symbol資料型態是搭配的,用於製造唯一鍵與變數的隱蔽性
let x = Symbol('sym');
myobj[x]=...

---

getter/setter
延伸變數存取的機能性

var myObject = {
    get a() {return this._a;},
    set a(val) {this._a = val * 2;}
};
myObject.a = 2;

---

檢查物件是否擁有某prop (不含prototype chain)
myObject.hasOwnProperty("a");

檢查物件是否擁有某prop (包含prototype chain)
("a" in myObject)

---

取得屬性描述
Object.getOwnPropertyDescriptor(myobj, "myvar");

設定屬性唯獨
Object.defineProperty(myobj, "myvar", {
    value: 5,
    writable: false,		//可寫入?
    configurable: false,	//可刪除?
    enumerable: true
});

禁止物件添加prop
Object.preventExtensions(myobj);

禁止物件添加/刪除prop
Object.seal(myobj);

禁止物件添加/刪除/修改prop
Object.freeze(myobj);

以上均為淺層(shallow)的設定

---

enumerable
列舉,相當於一個無序屬性的集合
能被列舉的對象表示能透過for-in歷遍(Traversals)
object與array的元素預設都是enumerable (若透過descriptor定義則預設是false)

for (key in myobj) {
    console.log(key + ' : ' + myobj[key]);
}

---

iterator
迭代器,一個能夠產生無須索引(key)資料的function().next(),例如產生亂數...
在JS裡iterator固定建立於myobj[Symbol.iterator]

iterator有延遲運算(Lazy evaluation)的特性,即call-by-need,
很適合做非同步運算(把大量串列運算切割分時執行)
這部分其實跟generator很相似

取得:
var arr = [1, 2, 3];
var iterator = arr[Symbol.iterator]();
iterator.next(); //{ value: 1, done: false }

自建:
myObject[Symbol.iterator]=function(){
	var idx = 0;
	var ks = Object.keys(myObject);

	return {
	    next: function() {
		return {
		    done: (idx >= ks.length),
		    value: myObject[ks[idx++]]
		};
	    }
	};
}
或透過defineProperty建立
Object.defineProperty(myObject, Symbol.iterator, {...});

---

Generator
類iterator(也有next()函數),在某些情境上程式碼會更精簡有彈性

function* hello_g() {
    yield 'hello'; // when step 0
    yield 'world'; // when step 1
}
var iter = hello_g();
for (var v of iter) {
    console.log( v );
}

---

Observable
可觀察物件.來自RxJS,補強很多JS相關於互動與非同步的操作.
從enumerable -> iterator -> generator,都跟處理串列資料有關
但又僅止於資料產生,RxJS的Observable的設計則更有應用情境.

---

for (let key in myobj/obj)	//與enumerable
Object或Array都可以用

for (let value of arr)	//object要有iterator才能用,或是object為一個generator
Array可以用,Object要自寫iterator

for (let [key, value] of map) //of map回傳的是陣列[key, value],所以可以這樣寫(解構)


arr.forEach(function(value, key, arr){...});

(記法 : in enumerable(有key) ; of iterator/generator/map)

---

Set Map

---

數學公式上
迭代：使用迴圈實作。有疊加的意思
遞迴：函式推疊呼叫。

---

@ 開頭是實例變數(instance variable)
@@ 開頭則是類別變數(class/prototype)

---

簡寫

let name="jim"
let myobj = {
	name,	//等同於name:name
	who()	//等同於who: function()
	{ return this.name }
}

---

Destructuring
解構,簡化變數宣告與指定

[]:以左右邊的順序依序指派
var [x, y, , z = 100] = [1, 2, 3];
for (let [key, value] of map)

{}:依key指派
var {w, h} = {x:0, y: 0, w:100, h:100};
var {w, h} = getSize();


---

mixin: 混和,僅一名詞. 指的是多重繼承,一般只有單一繼承,JS可透過修改Prototype達到多重繼承的效果

---







