---
title: __
---

[JavaScript DOM Crash Course](https://www.youtube.com/embed/0ik6X4DJKCc)

## 選取元件

```js
document.querySelector("input[type='submit']")
document.querySelectorAll(".myclass")
```

## innerText與textContent的差別
innerText能反應內元素display:none的變化,textContent僅是列出text node.


## Node/Element/HTMLElement

之間是繼承關係

- Node 在分類中包含TextNode
- Element 可以看作tag
- HTMLElement 即Element再包裹一層HTML特性,相對表示也有XMLElement

## JS

```js

el=document.createElement('div');
el=document.createTextNode('some text');

el.className = 'myclass';
el.style.border = "1px solid red";
el.setAttribute('type', 'none');

el.addElementListener('click', eventhandle);

```

## event type

```js
click dblclick
mouse~ down up enter leave over out move
key~ down up press
foucs blur
cut paste
input change submit


```

## event

```js
function eventhandle(e){
 e.target
 e.preventDefault() //停止預設事件對應

 e.clientX //滑鼠位置 相對於瀏覽器視窗
 e.offsetX //滑鼠位置 相對於e.target

 e.altKey
 e.ctrlKey
 e.shiftKey
}



```
