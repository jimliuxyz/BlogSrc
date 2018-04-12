---
title: __
---

[參考](http://blog.techbridge.cc/2017/05/20/api-ajax-cors-and-jsonp/)

- 同源政策，Same-origin policy。

- 資源的權限加註在http傳輸的header中 (Access-Control-Allow-Origin:*)

- 當用戶跨域請求時,需要設定crossOrigin參數,讓browser知道
    - null : 不設定(預設)
    - anonymous : 
    - credentials : 
    
browser則依據crossOrigin設定決定資源能否被使用 或僅能被怎樣使用

```js
  getBase64Image(url): Promise<any> {
    return new Promise((resolve, reject) => {
      var img = new Image();
      img.crossOrigin = "Anonymous";

      img.onerror = reject;
      img.onload = () => {
        console.log("img.complete:",img.complete)
        if (!img.complete) return;

        var canvas = document.createElement("canvas");
        canvas.width = img.width;
        canvas.height = img.height;

        var ctx = canvas.getContext("2d");
        ctx.drawImage(img, 0, 0);

        var dataURL = canvas.toDataURL("image/png");
        resolve(dataURL);
      };
      img.src = url;      
    });
  }
```

上述程式碼有兩個地方與CORS有關

1. 向伺服器請求圖檔 : anonymous與Access-Control-Allow-Origing的關聯. (jsbin的icon遇過資源沒設定ACAO時用anonymous反而會造成block)

2. canvas的toDataURL : 對資源的再處理也受到限制. 一般認知為資源ACAO為*時,設定anonymous即可使用資源.

### 麻煩
資源沒設ACAO,所以不能設anonymous,但沒設anonymous又不能用canvas的toDataURL. (Chrome)




