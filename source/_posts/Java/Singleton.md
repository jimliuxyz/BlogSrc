---
title: Singleton
date: 2018-04-06 12:21:55
tags: Java
---

[video](https://www.bilibili.com/video/av10771798/index_6.html#page=10)

建立的兩個需求:

- thread safe
- lazy

## Plain

```java
class MySingale{
    private MySingale(){}
    public static MySingale instance = new MySingale();
}
```

```kotlin
//kotlin
object MySingale{
}
```

## Lazy (not thread safe)
```java
class MySingale{
    private MySingale(){}
    private static MySingale instance;
    
    public static MySingale getInstance(){
        if (instance == null) instance = new MySingale();
        return instance;
    }
}
```

```kotlin
//kotlin
class MySingale{
    companion object{
        val instance by lazy(LazyThreadSafetyMode.NONE){
            MySingale()
        }
    }
}
```
 

## Lazy (thread safe)(synchronized)
- 加synchronized

```java
class MySingale{
    private MySingale(){}
    private static MySingale instance;
    
    public static synchronized MySingale getInstance(){
        if (instance == null) instance = new MySingale();
        return instance;
    }
}
```

```kotlin
//kotlin
class MySingale{
    companion object{
        private var instance: MySingale? = null
        
        @Synchronized
        fun getInstance(): MySingale{
            if (instance == null) instance = MySingale()
            return instance
        }
    }
}
```

## Lazy (thread safe) (volatile)
- 加volatile : 確保不同thread中能立即察覺變數的更動

```java
class MySingale{
    private MySingale(){}
    private static volatile MySingale instance;
    
    public static MySingale getInstance(){
        if (instance == null){  //避免持續進入synchronized
            synchronized (MySingale.class){
                if (instance == null){
                    instance = new MySingale();
                }
            }
        }
        return instance;
    }
}
```

```kotlin
//kotlin
object MySingale{
    companion object{
        val instance by lazy(mode = LazyThreadSafetyMode.SYNCHRONIZED){
            MySingale()
        }
    }
}
```


## 透過Holder (靜態內部類)
- 透過static Holder達到lazy
- new操作在java中原本也就是thread safe

```java
class MySingale{
    private MySingale(){}

    private static class Holder{
        static MySingale instance = new MySingale();
    }

    public static MySingale getInstance(){ return Holder.instance; }
}
```

```kotlin
//kotlin
object MySingale{
    companion object{
        fun getInstance() = Holder.instance
    }
    companion Holder{
        val instance = MySingale()
    }
}
```
