---
title: Kotlin
date: 2018-04-06 12:14:55
tags: Kotlin
---

[官方文件](https://www.kotlincn.net/docs/reference/basic-syntax.html)

## 簡介

- 介於Java與Scala之間

## 困擾

- 因為setter與getter緣故 難以區別'賦值'與'叫用' 看不出in func的移轉
- ()裡是什麼?
 - 前綴是class : 建構成員參數宣告
 - 前綴是fun : 函數參數宣告
- {} 裡是什麼?
 - 前綴是class : class宣告
 - 前綴是fun : fun宣告
 - 匿名函數 lambda


## 描述

- 不用分號結尾
- 一個檔案可多個類別
- 預設類別名稱 FilenameKt
- 沒有Primitive類型 全是Object (所以Java要存取都須透過getter/setter)
- 沒有static關鍵字
- @JvmStatic @JvmField 方便對映回java對應

## 關鍵字

- internal : 不允許外部訪問 (修飾class對java無效;但修飾fun會造成java無法access)

## Annotation

- @JvmOverloads //使fun多載(默認參數)
- @Synchronized
- @Volatile
- @JvmSuppressWildcards 避免Any轉成Java的?

## package

- 不宣告即為預設的package
- 檔案路徑不須對應package

## import

- 可以引入function

## 變數

- 前面一定要val或var
- val : 不可變(final)
- var : 可變
- lateinit : 告訴編譯器稍晚會init不要報錯 但若沒有init執行時仍會丟異常

```kotlin
var 變數名稱: 型態 ＝ 初始值
var x : Int = 1
var x = 1   //自動推定
var age_:Int? = null
x as String //轉型 (不需要再傳給變數)
x.trim()
```

- Int
- String
- Unit : 相當於void
- Any : 類似Object 但不能是null
- IntArray / intArrayOf(1,2,3)
- vararg : 相當於Java接收參數String args..., 用*vararg能將其轉回陣列與java互用

## Null

- 變數預設不能為null
- 型別後加?才能為null
- 安全調用 obj?.getNum()?:-1
- 空斷言!! 當null時報出exception (表示上:在變數後加!提示該變數是nullable)

```kotlin
var x: Any          //允許,但後續仍會被要求init
var x: Any? = null

fun getLength(s:String?):Int = s.length //編譯器不會過
fun getLength(s:String?):Int = s?.length?:0

fun getLength(s:String?):String = s!!

```


## function

- open : 可複寫
- 寫在最外層會變成 FilenameKt的靜態method

```kotlin
open fun myfunc(x: Int, s: String) : Int{
}

//單行回傳
fun getAge() = age
```

#### 匿名函數
- 不允許冠return 最後一行的變數或結果自動當作回傳

```kotlin
fun myfunc(type: Int, func: () -> Int){
    println(func())
}
myfunc(1,{7})   //熟悉的寫法
myfunc(1){8}    //較難理解的寫法
myfunc(func = {9}, type = 1) //指定參數位置

fun myfunc(func: () -> Int){
    println(func())
}
myfunc{10}      //很難理解的寫法 SAM conversion

//前面沒有fun關鍵字 後面接{}就是傳入匿名參數
```

#### 懶加載
- by lazy被用到時才初始化

```kotlin
val xy:Int by lazy {
    val x=1
    val y=5
    x+y //最後一行即為匿名函數的回傳
}
```

- 具名函數 有回傳須指定型態 且要冠return

```kotlin
fun func1():Int {return 1} //指名為func1
fun func2() {2}     //指名為func2
fun func3() = {3}   // {3}為匿名函數(lambda) 指定給func3作為回傳
fun func5() = 5

println("func1 ? " + func1())       //1
println("func2 ? " + func2())       //kotlin.Unit
println("func3 ? " + func3())       //Function0<java.lang.Integer>
println("func3' ? " + func3()())    //3
println("func4 ? " + {4}())         //4
println("func5 ? " + func5)         //5
```

```kotlin
//程式進入點
func main(args:Array<String>){
}
```

## string template

```kotlin
val x = 1

println("x = $x");
println("x+1 = ${x+1}");
```

## if-else

```kotlin
val max = if (x > y) x else y
```

## when
- if 也可以回傳值
- 後面接的是lambda

```kotlin
val x = 1
var y:Int
when (x) {
    1, 2     -> y = 1
    in 20..30     -> y = 1
    else    -> y = -1
}

//SmartCast 可回傳
var v = when (obj) {
    is String -> Integer.parseInt(obj)
    is Number -> obj.toInt() + 5
    else -> -1
}
```

## for

```kotlin
    for (i: Int in 1..5 step 2) {
        print(y)
    }
    //135
    
    for (i: Int in 5 downTo 1)
        print(i)
    //54321

```

## lambda

- it : 預設第一個引數

```kotlin
val ia: IntArray = intArrayOf(120, 30, 15, 27, 66)

ia.filter { it > 50 }         // 留下大於50的元素
    .sortedBy { it }            // 依照大小排序
    .map { it / 10 }            // 元素值除以10
    .forEach { print("$it-") }  // 顯示元素值
    // 顯示： 6-12-
```

## class

- open : 可被繼承
- close : 
- inner : 預設的inner class是static的(與java相反), 要成為member class需加inner

```kotlin
open class Person(var name: String) { // 首要建構子的參數列 (有寫就一定要call)
//參數列宣告有加var或val才能被外面存取

//首要(primary)建構子 (有寫就一定要call)
    init{
    }

//geter / setter
    var age:Int
        get() = age // java用.getAge()  kotlin仍用.age
        set(value) {age=value} //這樣寫會recursive,要用field取代age
        set(value) {field=value}

// 其它建構式，使用this(name)呼叫主要建構式
    constructor(name: String, , age: Int) : this(name) {
        this.age = age
    }

    fun sayHi() {
        println("Hi, $name!")
    }
}

//繼承
open class NewPerson(name: String) : Person(name){
    override fun sayHi() {
        println("Hey, $name!")
    }
}

val person = Person("Jim")
println(person.age)
person.sayHi()
    
```

## object 單例(Singleton)

- 立即實體化 沒有建構子 靜態實例
- 類似java的static 但access寫法不一樣
 1. 僅能透過ClassName存取 不能透過實例存取
 2. 中間多有一個單例的名稱
- 無法訪問其outter class的成員 (因為是單例/靜態/不屬於物件)
- 可以實作介面 但不能繼承 因為沒有建構子
- 可以宣告在任何地方
- companion只能定義在class中 且僅能有一個 類似於static

```kotlin
interface MyInterface { fun doit() }

object single : MyInterface{
    override fun doit() {
    }
}

class MyClass{
//static
    companion object{ //變數預設名稱為Companion
        var x=1
        fun myStaticFunc() {
        }
        //java叫用MyClass.Companion.sfunc()
    }

//單例 (class外部無法access)
    object mySingle{
        var x=1
        fun myFunc() {
        }
    }
}

//外界access方式 (不能透過類別實例存取)
MyClass.myStaticFunc
MyClass.mySingle.x
```
```java
MyClass.Companion.myStaticFunc
MyClass.mySingle.x
```

## 類別擴展

- 用途類似包裝utils工具 MyUtils.ext(person) 但更便利
- Java無法使用
- 有block範圍限制 不同block可有各自的擴展 (預編譯要先過)
- 可以靜態擴展 (可擴展第三方類別)
- 可以動態擴展 (但離開block後就不能用了)
- 可以透過import 在不同的檔案類別中共用

```kotlin
fun Person.ext(){
    println("are you $name ?")
}

import com.example.myextends.ext
import com.example.myextends.*

//泛型
fun <T> T.basicToString(i: Int) {
}
basicToString<String>(1)
```
- infix : 語法糖 便於閱讀

```kotlin
infix fun String?.append(s:String?) = this+s
var x = "xxx" append "dddd"
```

## 泛型
[video](https://www.bilibili.com/video/av10771798/index_14.html#page=12)
[*post](https://www.jianshu.com/p/1042ac6586ef)
[post](https://www.jianshu.com/p/0c2948f7e656)

- ? 相當於 * (這樣說不正確)
- extends 相當於 out get安全 set禁止 生產者
- super 相當於 in set安全 get禁止 消費者
- inline : 相當於將函數程式碼坎入到caller處 比較適用於lambda型式的函數
- refied : 與重新定義泛型有關

```java
//聲明處型變
interface Source<out T, in E> { // <<---在這裡
    fun funOut(): T //out表示僅能定義在輸出
    fun funIn(t:E)  //in表示僅能定義在輸入
}

//使用處型變
interface Source<T> {
    fun funIn(t: Source<out T>) // <<---在這裡
}

//泛型約束
<T extends String> ==> <T : String>
//kotlin允許多個約束 (用where關鍵字)

open class Food{}
open class Fruit : Food(){}
class Apple : Fruit(){}

```

- 不型變 指List<Apple>與List<out Fruit>是無關聯的
- 協變 指List<Apple>是List<out Fruit>的子類"型" (不是子類)
- 逆變 指List<Fruit>是List<in Apple>的子類"型"

突破java限制:

```java
//java
interface Source<T> {
    public T nextT();
}
public void demo(Source<String> strs){
    Source<Object> objs = strs; // 在Java中是不允许的
    // 正确方式为
    // Source<? extends Object> objs = strs;
}
```
```kotlin
//kotlin
abstract class Source<out T> {
    // 使用out的话，T只能作为返回值
    abstract fun nextT(): T
    // 不能作为传入参数，下面会报错
    // abstract fun add(value: T)
}

fun demo(strs: Source<String>) {
    val objects: Source<Any> = strs
}
```

## function

```java
interface Function <in T, out U>

Function<*, String> 表示 Function<in Nothing, String>
Function<Int, *> 表示 Function<Int, out Any?>
Function<*, *> 表示 Function<in Nothing, out Any?>
```

## enum 與 sealed

- sealed用法類似enum 但能以class或object識別

```kotlin
enum class Lang(val text: String){
    ENGLISH("Hello"),
    CHINESE("你好");
}
```


```kotlin
sealed class Cmd(val text: String){
    class Play(val url): Cmd
    object Stop: Cmd
}
```

## 委派 Delegate

- field存取委派 內部用get/set實現
- Delegates實作要繼承ReadWriteProperty
- 像是做lazy或監聽

```kotlin
var state:Int by Delegates.observable(0, {prop, old, new -> {
    println("$old => $new")
})
```

## 尾遞歸優化
- 避免StackOverflowError (其實就是轉為loop處理)
- 遞歸函數在最後呼叫稱為尾遞歸
- 函數前綴 tailrec

## Operator overloading
```kotlin
a == b  // a.equals(b)
a..b    // a.rangeTo(b)
a in b  // a.contains(b)
a[i]    // a.get(i)
a[i] = 9    // a.set(i, 9)
```

## 其他

```java
class MyClass{
}

Class::class.java //取得class實例 等同於Java的MyClass.class


```
