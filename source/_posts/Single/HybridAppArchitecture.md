---
title: Hybrid App Architecture
---


Cordova
======
其前身稱為PhoneGap 做為JavaScript Bridge 實現JS介接Native Library
[ref](https://dotblogs.com.tw/clark/2015/10/23/153681)

![](https://cordova.apache.org/static/img/guide/cordovaapparchitecture.png)

#### WebView
> 使用WebView意味著看到的ui元件不是native元件構建而成 而是透過webview畫出 (應該也沒有寫`NativeView`的必要了)

> WebView有分原生的或Cordova的跨平台WebView

#### Plugin大致寫法

類似用xml(metadata)分別設定android與ios 並分別實作api對應的native程式(繼承CordovaPlugin) 最後實作一個js object對應該api

```js
透過WebView叫用api (之後再自行封裝成類別形式)
cordova.exec(success_callback, error_callback, feature_classname, action_str, args);
```

React Native
======

[React Native 航向真全端，建構雙平台 App](https://ithelp.ithome.com.tw/users/20103341/ironman/1023?page=1) / 
[Native UI Components](https://facebook.github.io/react-native/docs/native-components-android.html) / [菜鳥窩React Native 從零到高級](https://www.jianshu.com/p/1c487ca546f1)
![](https://codebrahma.com/wp-content/uploads/2016/06/image001-e1467025274716.png)

只有JS Engine 沒有WebView 意味著web的ui元件實現須對應到native的ui元件上 實作比較辛苦 但執行比較快