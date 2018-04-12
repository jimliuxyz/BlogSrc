---
title: RecyclerView
date: 2018-04-04 19:36:16
tags: Android
---

## 加入RecyclerView

```kotlin
//0. 取得view
var view = findViewById<RecyclerView>(R.id.recyclerView)

//1. 設定layout
val layoutManager = LinearLayoutManager(this, LinearLayoutManager.VERTICAL, false)
view.layoutManager = layoutManager

//2. 設定adapter
view.adapter = MyAdapter(data)

//3. 設定分隔線
view.addItemDecoration(DividerItemDecoration(this, DividerItemDecoration.VERTICAL));

```

```kotlin
class MyAdapter : RecyclerView.Adapter() {
    override fun onCreateViewHolder(parent: ViewGroup?, viewType: Int): ViewHolder {
        //建立ItemView並存於ViewHolder回傳
    }
    override fun onBindViewHolder(holder: ViewHolder?, position: Int) {
        //將欲顯示的內容抽換到holder中的view
    }
    override fun getItemCount(): Int {
    }
 
    //自訂ItemView的Holder
    class ViewHolder(itemView: View) : RecyclerView.ViewHolder(itemView)
}
```