---
title: Animation
date: 2018-04-04 16:44:01
tags: Android
---

[*post](http://wiki.jikexueyuan.com/project/android-animation/)

## animation

1. property (透過修改view實例自動產生動畫)
2. view animation (tween) (Alpha Rotate Scale Translate Set)
3. drawable animation (frame by frame)

- 單位:
 1. 數字 : px
 2. 50% : 自己寬度的50%
 3. 50p : 容器寬度的50%

```java
//Tween

anim.setDuration
anim.repeatMode // 重頭再開始 / 倒退再開始

anim.fillAfter // 停在結束

anim_set.add(anim)

view.startAnimation(...)

anim = animationUtils.loadAnimation(R.id....) // from xml

aty.overridePendingTransition() //過場動畫

```
```java
//viewFlipper
filper.setInAnimation(...)
filper.setOutAnimation(...)
```
```xml
android:activityOpenEnterAnimation

<ViewAnimator
    android:inAnimation="@android:anim/slide_in_left">
    
    <TextView/>
    <TextView/>

</ViewAnimator>
```
-----------
[youtube](https://www.youtube.com/watch?v=cp-2Cl6OWRQ&index=60&list=PLHOqLxXumAI-hCYIzsnn6LCmZHzEStuuh)


## Animation

- Tweened (補間動畫)
 - alpha 淡入淡出
 - rotate
 - scale
 - translate 移動
- Frame-by-Frame
- Property Animation (屬性動畫)

> Tweened與ValueAnimator的差別 Tweened僅是繪製上的調整 也就是說移動一個button 事實上只是`畫到別的地方`而其屬性仍保留在原處 而ValueAnimator則不斷的調整view的屬性

## Tweened
```java
AlphaAnimation animate = new AlphaAnimation(1, 0);
animate.setStartOffset(1000);
animate.setDuration(1000);
animate.setFillAfter(true); //動畫後停留在最後
animate.setFillBefore(false); //動畫後回到起點
animate.setRepeatCount(1);

AnimationSet aset = new AnimationSet(true); //共用interpolator
aset.addAnimation(animate);

imageview.startAnimation(aset);
```

```java
//rotate需定義旋轉的中心點,中心點可相對於自己或containar
RotateAnimation animate = new RotateAnimation(0, 360
    Animation.RELATIVE_TO_PARENT, 1f,
    Animation.RELATIVE_TO_PARENT, 0f);
```
```java
//scale需定義縮放的軸心,軸心可相對於自己或containar
ScaleAnimation animate = new ScaleAnimation(1, 0.1f, 1, 0.1f
    Animation.RELATIVE_TO_SELF, 0.5f,
    Animation.RELATIVE_TO_SELF, 0.5f); //向中心點縮小
```

####用xml建動畫流程
建res/anim目錄,寫alpha.xml,android studio會自動產生對應的code在gen/R.java

```xml
<set ...>
    <alpha ... />
</set>
```
```java
Animation animate = AnimationUtils.loadAnimation(this, R.anim.alpha);
```

## Interpolator
[用途示範](https://www.youtube.com/watch?v=kNx83ejQLlY)
插值器 補插動畫效果

## Frame-by-Frame

```xml
<animation-list ...>
    <item android:drawable="..." android:duration="500" />
</animation-list>
```
```java
imageview.setBackgroundResource(R.drawable.animff);
AnimationDrawable ad = (AnimationDrawable)imageview.getBackground();
ad = AnimationUtils.loadAnimation(this, R.anim.alpha);
```

## LayoutAnimationController
控制layout group中動畫的順序

####從java建立
```java
Animation animate = AnimationUtils.loadAnimation(this, R.anim.alpha);
LayoutAnimationController lac = new LayoutAnimationController(animate);
lac.setDelay(0.5f);
lac.setOrder(LayoutAnimationController.ORDER_NORMAL);
listview.setLayoutAnimation(lac);
```

####從xml導入
```xml
<LayoutAnimation
    delay="0.5"
    animationOrder="normal"
    animation="@anim/alpha"
/>
```
```xml
<ListView
    layoutAnimation ="@..."
/>
```

## AnimationListener
```java
animation.setAnimationListener(new AnimationListener(){
    onAnimationStart()
    onAnimationEnd()
    onAnimationRepeat()
}
```

ValueAnimator
======

- ValueAnimator : 類似以值的變化形容動畫 類似補差值
- ObjectAnimator : 將ValueAnimator套用到Object上

```java
ObjectAnimator animator = ObjectAnimator.ofFloat(textview, "alpha", 1f, 0f, 1f);  
animator.setDuration(5000);  
animator.start();  
```
```java
//組合多個動畫
ObjectAnimator moveIn = ObjectAnimator.ofFloat(textview, "translationX", -500f, 0f);  
ObjectAnimator rotate = ObjectAnimator.ofFloat(textview, "rotation", 0f, 360f);  
ObjectAnimator fadeInOut = ObjectAnimator.ofFloat(textview, "alpha", 1f, 0f, 1f);  
AnimatorSet animSet = new AnimatorSet();  
animSet.play(rotate).with(fadeInOut).after(moveIn);  
animSet.setDuration(5000);  
animSet.start();  
```
```java
//在xml定義anim_file.xml
Animator animator = AnimatorInflater.loadAnimator(context, R.animator.anim_file);  
animator.setTarget(view);  
animator.start(); 
```

Activity
======
> overridePendingTransition方法需要在startAtivity方法或者是finish方法調用之後立即執行

```java
overridePendingTransition(R.anim.zoomin, R.anim.zoomout)
```