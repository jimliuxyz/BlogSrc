---
title: __
---

<style>
div.test div{
  margin:8px;
  padding:5px;
  border:.5px solid red;
}
</style>

## 前提知識

### 單位
#### 元件寬高
- px
- cm
- % 容器空間的百分比
- vh/vw/vmin/vmax : 可視空間的百分比。與%不同在於能撐開空白元件，類似px的行為。
- em : 上層元件字體的倍數
- **rem** : html元件字體的倍數

#### 其他
- fr : fraction.用於grid-template

## 控制內容物排版

- align-items：center (垂直置中)
- justify-content : center(水平置中)
- text-align:justify 使全形半形混用的文章頭尾對齊(左右平衡)

## 文字斷行

word-wrap跟word-break似乎有些重疊...

### word-wrap 單詞內斷行 (同overflow-wrap)

- normal
<div class='test'>
 <div style='width:50px'>aaaaaaaaa 123456789 中中中中中</div>
</div>

- **break-word 確保一定斷行,盡量保持行的開頭為單詞的開頭**
<div class='test'>
 <div style='width:50px;word-wrap:break-word'>aaaaaaaaa 123456789 中中中中中</div>
</div>

### word-break 單詞內斷行策略

- keep-all 不確保一定斷行,盡量保持行的開頭為單詞的開頭 (斷在符號)
<div class='test'>
 <div style='width:50px;word-break:keep-all;'>aaaaaaaaa 123456789 中中中中中</div>
</div>

- break-all 確保一定斷行,在任意字元都可以斷
<div class='test'>
 <div style='width:50px;word-wrap:break-word;word-break:break-all;'>aaaaaaaaa 123456789 中中中中中</div>
</div>

**MacDown無法順利顯示確實的效果**

## 文字column排版
[column](http://zh-tw.learnlayout.com/column.html)

## pistion

- static : 預設值，位置照版面推移
- fixed : 相對於瀏覽器視窗位置，不佔版面
- relative : 相對於當下位置，仍佔原版面空間
- absolute : 相對於relative parent(往上搜尋)，不佔版面

定位都用(top/bottom)與（left/right)，其值為與其相對物件的距離

## overflow
當容器有指定寬度時,就表示容器不會隨內容物縮放空間,就可能造成內容物溢出,overflow則是其對策
[eion.com.tw](http://www.eion.com.tw/Blogger/?Pid=1158)

- hidden / visible / scroll

## @media query

```css
//括弧中條件成立才apply 即width不超過640
@media screen and (max-width:640px) {
  nav li {
    display: none;
  }
}

//這種方式載入的css會與link載入的css會有時間差,畫面可能會閃爍
@import "style.css" screen and ...;
```

```html
<link rel="stylesheet" type="text/css" href="default.css" />
//screen handheld all ...
<link rel="stylesheet" type="text/css" href="print.css" media="print"/>
<link rel="stylesheet" media="screen and (min-width: 400px) and (max-width: 700px)" href="example.css" />

```
[ref](http://zh-tw.learnlayout.com/media-queries.html)


## @viewport

- 手持裝置瀏覽器有些不同於桌上型的特性,**通常是盡力將整個網頁呈現**
  - 內部有一個虛擬的viewport width(不是手機解析度)與scale值做自動調整
  - **viewport width指的是可視寬度**,即螢幕寬度對應的部分,scroll出去的不算
  - initial-scale的優先權比設定viewport width大(**實驗:設了scale,width就沒作用了**)
  - initial-scale=0.5其實是把viewport width*2.
  - **不設initial-scale再將body.width:100%取出document.body.offsetWidth即為viewport width**

- 因為這樣的特性使得畫面被縮小,不容易瀏覽與點擊操作,故做響應式設計(RWD)時會將scale設為1,讓layout能透過css的media query做調整,而不是直接被scale

```css
@viewport {
    width: device-width;
    /*width: 1024;*/
    initial-scale: 1;
}
```

```html
<meta name="viewport" content="width=device-width">
//關閉畫面縮放
<meta name="viewport" content="width=device-width, initial-scale=1.0, minimum-scale=1.0, user-scalable=no">
css file:@viewport{}
```




