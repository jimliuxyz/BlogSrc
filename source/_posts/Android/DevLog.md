---
title: Android Dev Log
date: 2018-04-04 19:39:45
tags:
    - 開發紀錄
    - Android
---

## TextView行高在編輯文字時會改變

```kotlin
addTextChangedListener(object:TextWatcher{
    override fun afterTextChanged(s: Editable?) {
    }
    override fun beforeTextChanged(s: CharSequence?, start: Int, count: Int, after: Int) {
    }
    override fun onTextChanged(s: CharSequence?, start: Int, before: Int, count: Int) {
        //每次文字更動時都在設定一次行高
        setLineSpacing(0f, 1f)
        setLineSpacing(20f, 1f)
    }
})
```
