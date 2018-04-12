---
title: view 與 Style
date: 2018-04-04 15:37:31
tags: Android
---

<span class="inline-path">/Users/jimliu/Library/Android/sdk/platforms/android-26/data/res/values/styles.xml</span>


## 容器類型的view
- ListView : 列表顯示
- RecyclerView : 列表顯示 更為靈活 取代ListView
- ScrollView : 可滑動的顯示
- gridview / gallery / cardview

### LayoutInflater
> layout的實體化工具 將layout resource轉成view的實例

### Adapter
> 設計模式中稱作"適配器" 簡單的說就是轉換器 以view容器的應用來說 就是將'資料數據'轉換填充到'ListView裡的各個item view'上

```java
//以simple_list_item_1做為item view
adp = new ArrayAdapter<String>(this, android.R.layout.simple_list_item_1);

//替adp添加兩筆資料
adp.add("first");
adp.add("second");

//將adp指定給listview使用
lv = findViewById(R.id.listView);
lv.setAdapter(adp);
```
### BaseAdapter

```java
@Override
public View getView(int pos, View convertView, ViewGroup parent){
  //getView用於取得item view
  //convertView : 先前已建立的view,用於優化效能
}

notifyDataSetChanged(); //向view通知data有變動 view才會refresh
```

#### ViewHolder
> 主要用意在復用item view時避免重複findViewById
```java
v.setTag(holder);
holder = (Holder) v.getTag();
//RecyclerView.Adapter不用這樣寫
```

## SurfaceView
> 有自己的ui thread 用於較佔時間繪圖應用 (VideoView/ImageView)

## View狀態的save與restore

1. view需要設定id才能被儲存
2. android:saveEnabled 預設已為true

## 自定義View / ViewGroup
> 先分清元件與佈局的角色

#### 大致
1. 需要複寫onMeasure (父要求子做元件尺寸測量`measureChildren`) (子則量測自己) 量測完執行`setMeasuredDimension`
2. ViewGroup需複寫 onLayout (排定子的繪出位置) 逐個叫用`child.layout`
3. View需複寫 onDraw (繪製本身)

#### MeasureSpec定義
> 為一整數 前3bit做為mode 之後作為size(寬或高) 做為父要求子做尺寸量測時的參考 (由父提供spec 子做量測)

- UNSPECIFIED : 沒有任何約束
- EXACTLY : 約束明確的size
- AT_MOST : 約束上限的size

#### LayoutParams
> 一個ViewGroup要遍歷所有子View的LayoutParams才知道每個子View要求被如何佈局 (所以是ViewGroup定義的 但由子View描述給其ViewGroup參照的) `child.getLayoutParams`


## Theme與Style
> aad:Construct a layout using XML or Java code

> aad:Create a custom view class and add it to a layout

> aad:Implement a custom application theme

> aad:Apply a custom style to a group of common widgets

> 兩者僅有使用廣度上的差別 theme針對app或activity, style針對元件, 基本上都是style

#### 單位
![](http://1.bp.blogspot.com/-k8iDcrGHmz4/UfCrsfwyoPI/AAAAAAAADJA/Kvpga5ZH1Fc/s1600/pixel-density-2.png)

dp : 1dp=160ppi的px長度 (像素隨密度改變)
sp : 類似dp 用於文字
pt : 點 用於文字
px : 像素
ppi, dpi : 像素密度 pixel per inch 每英寸像素數
mm : 毫米
in : 英吋

#### attr宣告
> 寫在values/attrs.xml 作為元件的屬性 (元件屬性是從xml建立的 java code僅是依資源ID取值賦予元件)

> attr及所有資源都會自動產生於R類中的id

```xml
<attr name ="mycolor" format= "color" />
```
> 屬性名稱一多容易重複混淆 用declare-styleable可將其分類 java code也會比較好讀取

```xml
//將多個attr並於declare-styleable有利組織化與讀取 在R類中會是一個resId的陣列
<declare-styleable name="MyStyle">
    <attr name ="mycolor" format= "color" />
<declare-styleable>
```
#### attr的賦值

```xml
//在元件標籤中賦值 (前半部需指定命名空間)
<TextView
    app: mycolor="#00ff00"
    android:style="@style/MyStyle">
</TextView>
```


```xml
//在style/theme中賦值
<style name="AppTheme" parent="Theme.AppCompat.Light.NoActionBar">
    <item name="mycolor">#0037ff</item>
    <item name="android:textViewStyle">@style/textviewstyle</item>
</style>
```

#### 元件取得attr的來源

>一個元件的一個attr可能有多個來源 (大致的優先順序)

1. 於元件標籤中或標籤中指定的style有描述
2. 從application或activity指定的theme中有描述
3. theme中指定的style有描述 (即指android:textViewStyle)(format="reference")
4. 元件hardcode裡有預設的style

#### 元件取得attr obtainStyledAttributes (AttributeSet set, int[] attrs, int defStyleAttr, int defStyleRes)

- AttributeSet 即元件標籤中的描述
- attrs 欲取得的attr
- defStyleAttr 預設的style的attr (在theme中經由該attr參考到確切的style)(即指android:textViewStyle) 透過這樣的機制就可在theme中複寫掉元件的整個style
- defStyleRes 預設的style 若沒有可丟0

```java
public CustomView(Context context, AttributeSet attrs, int defStyleAttr) {
    this(context, attrs, defStyleAttr, 0);
    TypedArray a = theme.obtainStyledAttributes(attrs,
            R.styleable.MyStyle, defStyleAttr, defStyleRes);
    int color = a.getColor(R.styleable.MyStyle_mycolor, Color.BLACK);
    a. recycle();
}
```

## 可見性的設定

View.visable
View.invisable : 看不見 但仍佔位置
View.gone : 消失 不佔位置

## 模糊視窗(blur)

```java
getWindow().addFlags(WindowManager.LayoutParams.FLAG_BLUR_BEHIND); 
```

## ViewStub
一個空的view 用來延遲載入另一個view時當作佔位用

#### SpannableString
> v.setText()通常傳入字串 更複雜可以傳入(html, img_getter, tag_handler) 另一種是傳入SpannableString 用途都是達到圖文混排

其他
======

#### onTouch 和 onTouchEvent
> onTouch優先於onTouchEvent 若onTouch回傳true則事件就不會再往下傳播
> onTouch是作用於view處於enable狀態下