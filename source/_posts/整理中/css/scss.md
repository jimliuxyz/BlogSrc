---
title: __
---

[參考](http://blog.kdchang.cc/2016/10/11/sass-scss-tutorial-introduction/)

[SCSS to CSS online](https://www.sassmeister.com/)

[內建函數](http://sass-lang.com/documentation/Sass/Script/Functions.html)


```scss
//變數宣告
$tag:body;
$resizer-size:15px;
$list: (orange, purple, teal);
$font-style:(
    family: sans-serif,
    size: 15px
);

//載入外部scss
@import "../node_modules/bootstrap-4.0.0-beta/scss/bootstrap";

//在selector使用變數 #{...}
#{$tag} {
}

//maps變數讀取
map-get($font-style, size);

```

```scss
//函數
@function func($var) {
  @return 10;
}
```

```scss
//混入 用法類似函數 沒有回傳值 而是混入css
@mixin mixfont($family, $size:10px) {
}
body {
    @include mixfont(sans-serif, 10px);
    @include mixfont($font-style...);
}

```

```scss
//運算元 + - * /
@each $item in $list {...}
@for $i from 1 through $total {...}
```


```scss
//巢狀
.parent {
  .child {  // .parent .child{}
  }
}
.parent {
  font : {
  
    family: sans-serif;
  }
}
.parent { 
  &:hover {
  }
  &:dialog { // .parent.dialog{}
  }
}

```

```scss
//繼承
.A{
}

.A1{
    @extend .A;
}

//placeholder
div%B {
}

.test {
	@extend %B;    //產生div.test
}


```