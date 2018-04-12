---
title: Layout
date: 2018-04-04 17:46:56
tags: Android
---

------
Layout
======

> view跟layout定義上的差別: view是通常形容元件 layout則是容器(容器內的排版)
> layout是view的子類

### RelativeLayout
定位於 某元件 或 所處容器 的相對位置

### FrameLayout
定位於左上角 (view會重疊)

### LinearLayout
水平或垂直 接續排列 權重分佈

### TableLayout
格狀排列

### ConstraintLayout
約束排列 約束元間之間的相對關係 (取代RelativeLayout)


```java
LayoutParams.FILL_PARENT //依父容器大小 填滿父容器
LayoutParams.WRAP_CONTENT //依內容大小 適當顯示
```

### SwipeRefreshLayout
有下拉Refresh功能

```kotlin
findViewById<SwipeRefreshLayout>(R.id.refresh_layout)?.apply {
    isRefreshing = true //設定為正在refresh
    setColorSchemeColors(
            ContextCompat.getColor(activity, R.color.colorPrimary),
            ContextCompat.getColor(activity, R.color.colorAccent),
            ContextCompat.getColor(activity, R.color.colorPrimaryDark)
    )

    setOnRefreshListener{
        println("do refresh...")

        postDelayed({
            println("done refresh...")
            isRefreshing = false //設定為沒在refresh
        }, 3000)
    }
}
```

