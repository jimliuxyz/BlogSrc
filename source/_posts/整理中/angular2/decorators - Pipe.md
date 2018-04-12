---
title: __
---

功能形容:

```html
<p>{{ birthday | mydate:fmt }}</p>
```
輸出 April 15, 1988 而非 Fri Apr 15 1988 00:00:00 GMT-0700
其中的mydate就是一個pipe元件

---

又分pure跟impure
區別在pure是在birthday更動時運作,impure則在任何的component change detection cycle運作
是需求選用

---

範例
```js
@Pipe({
  name: 'mydate',
  pure: true
})
export class MyDatePipe implements PipeTransform {
  transform(value: number, fmt: string): string {
    return ...;
  }
}
```
