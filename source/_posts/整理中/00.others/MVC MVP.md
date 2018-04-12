---
title: __
---

[video](https://www.youtube.com/watch?v=RTmhBLc6B-U)
[ref](https://cg2010studio.com/2016/06/02/mvc%E8%88%87mvp/)

![](https://cg2010studio.files.wordpress.com/2016/05/mvce88887mvp.gif?w=540&zoom=2)

![](https://cg2010studio.files.wordpress.com/2016/05/mvc-original.png)
![](https://cg2010studio.files.wordpress.com/2016/05/mvc-apple.png)
![](https://cg2010studio.files.wordpress.com/2016/05/mvp.png?w=1080)

- M : Model : 資料
- V : View : 畫面
- C : Controller
- P : Presenter

## MVC
> C僅負責接收V的要求 並安排後續處理流程 但M處理的結果不再經由C回給V

V -> C -> M....-> V

## MVP
> V與M互動完全需要透過P 與MVC的差別主要在M到V的部分是完全分離的

V <-> P <-> M

MVP的架構會比較好 因為MVC中M回給V的資料畢竟最後透過V顯示 而M有必要替V預處理好便於顯示的資料嗎? 還是V自己再處理資料? 不管哪種好像都有些超出他們該負責的業務

## MVVM
> 將P調換為VM


## Coding

- View與Model的部分建議用interface隔開實際類別 一來乾淨 二來不至於讓自己打亂架構

```java
interface MainView{
}

class MyActivity extends Activity implements MainView{
}

class Presenter{
    Presenter(MainView v){
    }
}
```

- 盡量使用依賴注入 一來便於解偶 二來便於測試時抽換

```java
class Presenter{
    Presenter(MainView v, Model m){ //<---依賴的部分來自外部
    }
    Presenter(MainView v){
        m = new ModelDemo(); //<--- 外部無法抽換Model
    }
}
```

## 流程

- 先建interface / data class
- 建view
- 架構骨幹簡易畫面
- 稍加測試
- 實現interface

## 資料流情境

```java
data class Book{
    author: String
    bigText: String
}
```

```java
class View{
    h = Handler() //Android Handler (main thread execution queue)
    p = Presenter()
    
    onUserClick(){
        //同步
        p = p.cmd(arg); // set value / do things
        rv = p.get(arg);
        
        //異步
        rv = p.getWithCallback(arg, this);
        rv = p.getWithCallback(arg, { println("done") });
    }
    
    onPresenterCallback(){
    }
    
    private changeViewComponent(){
        //must run at main thread
    }
}
```




