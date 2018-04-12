---
title: __
---

## 前提知識

### Element (Box)
一個元件的完整空間由margin border padding content組成

- content 內容物
- border 邊線
- padding content與border的間距
- margin 元件間的間距 兩元件取margin大者做為間距 **常做為元件垂直置中的手段**
- margin可以只撐開外容器 padding可以撐開自身(進而影響外容器) **但在inline元素中兩者的margin-top/margin-bottom都強制為零(除非是inline-block)**
- 補充
 - block元件間的margin是border to border,但inline元件是content to content(不是很重要)
 - 參數順序：上下 左右

### box-sizing
決定width/height的組成成員。以border決定空間的border-box顯得更直覺。

- content-box 預設值
- border-box (border+padding+content)

### inline / block
- inline 行內元素
- block 換行(列)元素,**預設有左右撐開佔滿的特性(就算沒有內容)**
- inline-block 對外是inline,但對內是block(可以被撐開,外容器空間會改變)
- none 不顯示且不佔版面(visiability:hidden會佔版面)

<style>
div.test div{
  margin:8px;
  padding:5px;
  border:.5px solid red;
}
</style>

<div class='test'>
 <div style='display:inline;margin:100px;'>inline</div>
 <div style='display:block'>block</div>
</div>


### 容器概念
- 容器 wrapper / container
- 內容物 item

元件的排列從視覺上像是挖空往內塞,但實際上是一層一層**'往上'**推疊. (所以處理不當時內容物才會暴露到容器外)

設計容器有兩種角度(以高度舉例)

- 容器有確切的高度或比例
 - 由外層向內層定義
- 內容物有確切高度
 - 由內層向外層定義
 - **容器最後的大小是從內向外撐開的**
 
容器一但指定了height(px或%或其他),就不會被內容物撐開
容器若沒有指定height,內容物用%時就會參考到更上層有指定height的容器作分子

沒有指定height的情況通常是希望瀏覽器內部能自動排版位置與空間,像是用了display:flex,但層層的容器中終究會遇到要指定寬高的內容物,通常會想設定容器的n%,但卻發現容器被撐破了.

以下是一個使wrapper撐破與修復的範例. 修復的方式是在要設定寬高的層中加入absolute,並在外容器加上relative對應,這算是relative另一個很大的用處.

```html
<div class='wrapper'>
  <div class="flowbox">
    <div class="box1">1</div>
    <div class="box2">
      <div class="fullbox">fullbox</div>
    </div>
  </div>
</div>
```
```scss
* {
  box-sizing:border-box;
}
.wrapper{
  width:100px;
  height:100px;
  background-color: gray;
}
.flowbox{
  height:100%;
  width:100%;
  display:flex;
  flex-flow:column;
  border:2px solid blue;

  .box1{
    margin:5px;
    border:2px solid green;
  }
  .box2{ //沒有指定height
    margin:5px;
    border:2px solid green;
    flex:1;
    /* position:relative; //fix */
  }
}

.fullbox{
  width:100%;
  height:100%;  //要參考box2的height
  background-color: pink;
  /* position:absolute; //fix */
}
```

---

### float:left/right
float的概念是將元件A抽離原本的位置,讓給下一個元件B,而自身則轉移到另一個xy相近但z軸更高的容器上,造成AB外框重疊,但content不重疊的特性,也就是B元件的內容會避開A元件,常見的應用就是文(B)繞圖(A)。
另一種應用就是拿來做選單,只想取其使元件水平排列的特性,但不想造成B被A覆蓋的情形,就會在B做clear的動作,使B整個移動出float容器的遮擋。這應該算過渡時期的用法，CSS3後多用flex做選單。

- 透過float可將block元件的流動調整為水平的
- float可以自動換行


## 自適應式Layout (RWD)

### display:flex

### flex (wrapper)
- flex-flow : 流動方向(main-axis) **row(水平流)** colume
- flex-wrap : 換行 nowrap wrap
- justify-content : 配位(main-axis) flex-start flex-end center space-between space-around
- align-content : 配位(cross-axis) flex-start flex-end center space-between space-around stretch
- align-items: 對齊(cross-axis) flex-start flex-end center baseline stretch

### flex (item)
- flex-grow : 空間權重(有設定時容器才會撐開,一律只能左右撐開)
- flex-shrink : 空間權重(當wrapper被縮的比固定px還小時)
- flex-basis : 最小空間(px，通常設auto，設0會變成能放不能縮)
- flex : 結合上述三者一起設定
- align-self : 脫離wrapper自己對齊
- order : 調整順序 數字越小越前

---

### display:grid

### grid (wrapper)
- **grid-template-columns(水平流)** grid-template-rows<br>
分別指定一個CxR的grid line規範,line可以命名,單位 px fr % atuo,函數repeat
- grid-template-areas<br>
定義區塊area name
- grid-gap : 間隔
- justify-content align-content justify-items

```css
.grid-wrapper {
  display: grid;
  grid-template-columns: [gc1] 100px [gc2] 1fr [gc3] 2fr [gc-end];
  grid-template-rows: [gr1] 100px [gr2] auto [gr-end];
  grid-template-areas: "r1c1 r1c2"
                       "r2c1 r2c2"
                       "footer footer";
}
```

> flex裡的item空間使用％時會對應到body甚至完全無法對應 <br>
> 必須在item裡做position:absolute,外容器做position:relative <br>
> 讓item重新定位到reference parent

### grid (item)
- grid-column : 以數字或名稱指定column的start/end
- grid-row : 以數字或名稱指定row的start/end
- grid-area : 以area name指定區塊
- justify-self align-self

```css
.grid-item{
  grid-column: gc2 / gc-end;
  grid-row: gr2 / gr-end;
  grid-area: footer;
}

```





















<script>
var x=document.getElementById('xxx');
x.innerText='哈哈';
</script>
