---
title: ArchitectureComponent
date: 2018-04-04 17:24:29
tags: Android
---

[video](https://www.youtube.com/watch?v=kdcqXOhBivE)

[video](https://www.youtube.com/watch?v=ai6kKsC5JnY)


- LifecycleOwner : lifecycle的擁有者 像activity或fragment都是LifecycleOwner 可透過getLifecycle()取得Life

- LifecycleObserver : 一個介面 實作這個介面 就能透過Life.addObserver加入監聽lifecycle事件的行列

- ViewModel : 