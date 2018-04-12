---
title: Fragment
date: 2018-04-04 19:34:06
tags: Android
---

![](https://i1.kknews.cc/SIG=v8aj42/16p200037qo274368116.jpg)

## 加入Fragment
- fragment要被砍入的位置 R.id.contentFrame
- fragment自身的佈局 R.layout.main_frag

```kotlin
var fragment = supportFragmentManager.findFragmentById(R.id.contentFrame)?:MainFragment().also {
    supportFragmentManager.beginTransaction().apply {
        add(R.id.contentFrame, it)
        commit()
    }
}
```

```kotlin
class MainFragment : Fragment(){
    override fun onCreateView(inflater: LayoutInflater?, container: ViewGroup?, savedInstanceState: Bundle?): View? {
        var view = inflater?.inflate(R.layout.main_frag, container, false)
        return view
    }
}
```
