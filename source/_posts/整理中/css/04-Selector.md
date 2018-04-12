---
title: __
---

## 基礎

```css
// OR |
img, a       => <img> <a>

// AND &
div.cls      => <div class='cls'>
.cls1.cls2   => <div class='cls1 cls2'>
```
## Attribute Selector

- = 完全匹配
- ^= 字串開頭匹配
- |= 類似^但更精準,例如btn只能匹配'btn'與'btn-',如果是'btn1'就不行
- ~= 字串以空白切割後完全匹配
- *= 有出現即可匹配
- $= 字串結尾匹配

```css
//匹配div中有status-開頭的class
div[class^="status-"], div[class*=" status-"]
```
[測試頁](https://codepen.io/terkel/pen/ywEth)

## 

## Combinator

- .cls + * : 選到cls的nextSibling (僅next一次)
- .cls ~ * : 選到cls的all nextSibling (next全部)

- .cls * : 選到cls的所有後代 (遞迴多層)
- .cls > * : 選到cls的子層 (僅一層)


## Form Selectors

## 偽元素選擇器
一個依附在某元素的假元素


## 偽類選擇器
一個依屬性產生的css class
