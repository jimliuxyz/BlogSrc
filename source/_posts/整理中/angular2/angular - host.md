---
title: __
---

[ref](http://www.itread01.com/articles/1490788874.html)

- host : directive都是依附在dom物件上運作, host則指其所依附的dom物件

```js
//button-press.directive.ts
import { Directive, HostBinding, HostListener } from '@angular/core';

@Directive({
    selector: '[exeButtonPress]'
})
export class ExeButtonPress {
    @HostBinding('attr.role') role = 'button';
    @HostBinding('class.pressed') isPressed: boolean;

    @HostListener('mousedown') hasPressed() {
        this.isPressed = true;
    }
    @HostListener('mouseup') hasReleased() {
        this.isPressed = false;
    }
    
    @HostListener('mouseup',['$event']) hasReleased2(event) {
        this.isPressed = false;
    }
}

//button-press.directive.ts (another way)
@Directive({
    selector: '[exeButtonPress]',
    host: {
      'role': 'button',
      '[class.pressed]': 'isPressed'
    }
})
export class ExeButtonPress {
    isPressed: boolean; // you have to declare here also
    ...
}

//app.component.ts (how to use it)
import { Component } from '@angular/core';

@Component({
  selector: 'exe-app',
  styles: [`
    button {
      background: blue;
      color: white;
      border: 1px solid #eee;
    }
    button.pressed {
      background: red;
    }
  `],
  template: `
    <button exeButtonPress>按下按鈕</button>
  `
})
export class AppComponent { }
```