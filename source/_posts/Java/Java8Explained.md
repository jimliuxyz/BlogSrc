---
title: Java 8 增訂
date: 2018-04-06 15:42:02
tags:
    - Java
---

## Lambda 箭頭函數
lambda是一個java8開始的新語法 類似於javascript的箭頭函數 因應functional programming而生

可以將箭頭函數想像成一個"只有一個method的object" 就會比較容易理解 看/用起來像function但其實是一個object 因為只有一個method所以method的name就不重要了 object即等同method

為什麼要這樣?因為java的method不能像變數一樣當作參數 要傳函數勢必只能整個object傳入 而箭頭函數就是為了解決這樣的困擾

```java
Runnable runnbale = new Runnable() {
    public void run() {
    	System.out.println("run me!");
    }
    public Runnable get(){
        //將現有的method當作箭頭函數般回傳
        return this::run;
    }
};

//箭頭函數的形式
Runnable runnbale = () -> System.out.println("run me!");
```
## Collection走訪

functional programming的另一個重點就是function chain與Collection(list/map/array)的走訪. 搭配`Collection的stream()`

```java
//透過stream()衍生function chain
list.stream().filter(s -> Integer.valueOf(s) < 3).forEach(s -> System.out.print(s));

//parallelStream() : 平行處理的stream
```

## Lambda 以函數為參數
```java
//格式
Function<input, output>
Consumer<input, void>
Predication<input, boolean>
Supplier<output>
```

```java
static test(int x, Function<int, string> fun){
    f.apply(x);
}

test(99, (int x) -> {return "99"});
```


## Interface擴展
interface的method可前綴default與static的關鍵字 用以擴展interface的method

## Optional
[link](http://blog.tonycube.com/2015/10/java-java8-4-optional.html)

Optional是一個新的類別 用於封裝一個傳入或回傳值 並提供一些良好的方法 讓programer養成良好的習慣

## Data/Time方面的新類別