---
title: Widget
date: 2018-04-04 16:52:02
tags: Android
---

## Widget
> 一個自訂的service(app)顯示且整合於WorkSpace中 整合困難的部分在於launcher與widget執行於兩個不同的process中

- AppWidget 即自訂的app
- AppWidgetHost 一個AppWidget的宿主 負責與AppWidget交互
- AppWidgetHostView

- AppWidgetManager
- AppWidgetProvider 提供view的更新 是一個BroadReceiver (即使是空的也無所謂)
- AppWidgetProviderInfo 提供資料的更新(mata-data)

> 寫code只需要AppWidgetProvider與Manifest+provider_info+layout的設定即可

#### RemoteViews
> 一個能夠對映view的投射物件(兩者都從相同的layout建立) 透過RomoteViews操作view 再將此RomoteViews丟到遠端 遠端即可知道如何更新實際的view 達到跨process的view操作