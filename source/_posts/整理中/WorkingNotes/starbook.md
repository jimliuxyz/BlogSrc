---
title: __
---

1+2=3 \
1+3=4

#### ?以下何者大於10?

- 敘述1
- 敘述2
1. =14
1. 1
1. -3

解題

---

//https://translate.google.com.tw/translate_a/single?client=t&sl=en&tl=zh-TW&dt=t&ie=UTF-8&oe=UTF-8&q=who%20are%20you
//https://ctrlq.org/code/19899-google-translate-languages#languages
//https://translate.google.com/translate_tts?ie=UTF-8&client=tw-ob&tl=zh-TW&q=測試


console.clear();

var sourceLang="zh-TW"
var targetLang="en"
var sourceText="我愛妳"
  var url = "https://translate.googleapis.com/translate_a/single?client=gtx&sl=" 
            + sourceLang + "&tl=" + targetLang + "&dt=t&q=" + encodeURI(sourceText);
  
 // var result = JSON.parse(fetch(url).getContentText());

//console.log(result);
  
fetch(url).then(res=>{
//   console.log(res.json())
      res.json().then(function(data) {
      console.log(data);
    });
})

---


