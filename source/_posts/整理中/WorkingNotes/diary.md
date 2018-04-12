---
title: __
---


# Resizer
一個看似單純的東西竟然寫了整整兩天,四個版本

- 原本用每次move的delta推動,但體驗上會出現cursor與resizer產生距離,必須用drag start event與move event的差,而非每次move event之間的差

- 多級運作時沒辦法推動下一級,與推動後要定位

- 原本用px運作,忘了resize擴大時會無法填滿

---

# 載入CSS時機
以angular只有單一html入口的情況,如果在A/B兩網址(非SPA)用上不同的‘body bgcolor?
- 非原生存在於.html的style的插入或變更都可能使畫面抖動
- 最後只能從server端吐出對應好的css style

> 可以嘗試用binding，<link rel=... href="{BOOTCSS}">讓server吐不同的href

```
<link id="theme" rel='stylesheet' href='assets/demo.css'>
//---
    $('body').addClass('fullbody')
//--
    var style = document.createElement('link');
    style.setAttribute('rel', 'stylesheet');
    style.setAttribute('href', 'assets/demo.css');
    document.querySelector('head').appendChild(style);

```

---

# 實現方式太多
驚覺常常進入放空狀態，不知從哪下手比較正規

---

# bootstrap

```
npm install bootstrap@3 --save
或
npm install bootstrap-sass@3 --save

        "../node_modules/bootstrap-sass/assets/stylesheets/_bootstrap.scss"
        "../node_modules/bootstrap-sass/assets/javascripts/bootstrap.js"
```

```
$icon-font-path: '../node_modules/bootstrap-sass/assets/fonts/bootstrap/'; //fix path error

@import "assets/variables";	//a copy to override bootstrap variables
@import "../node_modules/bootstrap-sass/assets/stylesheets/_bootstrap";
```

---







