---
title: __
---


## ionView
- DidLoad
- WillUnload

- Enter
    - Will
    - Did (become the active page)
- Leave
    - Will
    - Did (no longer the active page)

- CanEnter / CanLeave

類似於iOS的life cycle

![lifecycle](https://cg2010studio.files.wordpress.com/2013/05/uiviewcontroller-lifecycle.jpg)

## view轉換

FIRST TIME VIEW INITIALIZATION

- View 1 – DidLoad
- View 1 – WillEnter
- View 1 – DidEnter

TRANSITION FROM ONE VIEW TO ANOTHER

- View 2 – DidLoad
- View 2 – WillEnter
- View 1 – WillLeave
- View 2 – DidEnter
- View 1 – DidLeave

## 換頁

[NavController](https://ionicframework.com/docs/api/navigation/NavController/)

[Tab](https://ionicframework.com/docs/api/components/tabs/Tab/)

```js
//page1.html
<ion-item navPush="page2" [navParams]="toUser">

//page1.ts
toUser:any={toUserId:'somebody'};

//page1.ts
this.nav.push(page2, toUser);

//page2.ts
constructor(navParams: NavParams) {
  this.toUserId = navParams.get('toUserId');
}

```




