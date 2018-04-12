---
title: Java
date: 2018-04-06 12:17:09
tags: Java
---
[面試題](http://wiki.jikexueyuan.com/project/java-interview-bible/)

field修飾字
=====
||同package|子類|其他package|
|-----|:-----:|:-----:|:-----:|:-----:|
|public|✅|✅|✅|
|protected|✅|✅|×|
|default|✅|×|×|
|private|×|×|×|

引用
======

- 強引用(Strong)
> 強行使物件無法被回收

- 軟引用(Soft)
> 不置於立即回收 做cache時很適合

- 弱應用(Weak)
> 同軟引用 但更容易會被回收

- 虛引用(Phantom)
> 該回收就回收 可用於檢查物件的回收與否

```java
person = new Person("john", 10);
ref = new SoftReference<Person>(person);

Person p = ref.get();
```

String
======
透過`純字面`建立的string會在靜態緩衝裡建立快取 所以同樣的字串會對應到同樣的位置 但透過new String則一定是一個全新的指向
```java
s1 = "123";
s2 = "12" + "3";
s3 = "12" + new String("3");
s4 = new String("123");
// 其中只有s1==s2
```

StringBuffer / StringBuilder
======
- StringBuffer : thread-safe 較慢
- StringBuilder : not thread-safe 較快
> builder類通常宣告於method內做local變數 沒有共用的問題 也就不需要thread-safe

interface / abstract
======
> interface
- 一個interface可繼承(extends)另一個interface
- 一個class可實作(implements)多個interface
- 可有member field 但強制成為static final
- method上加註default或static建立具體method (java8開始)

> abstract 無異於一個具體類(concrete class) 但無法實例化 且有抽象方法
- 一個class僅能繼承一個abstract class

IOC / DI
======
> 控制反轉(Inversion of Control) 等同依賴注入(Dependency Injection)
> 即指建立一物件時 其依賴並非由`建立者`或`建立物`來建立提供 而由一專屬框架負責


InputStream / OutputStream
======
> 流操作分兩類 byte/character
- byte : 以InputStream / OutputStream結尾
- character : 以Reader / Writter結尾

以InputStreamReader為例 前綴InputStream表示輸入為byte 後綴Reader表示輸出character

![](http://wiki.jikexueyuan.com/project/java-interview-bible/images/stream.jpg)

Proxy 代理
======
> 即中間人 透過中間人與物件互動 中間人即可提供一些額外的功能

- RealSubject 被代理的物件(委託對象)
- ProxySubject 代理對象
- Subject RealSubject與ProxySubject共同的介面 假設有一個request方法

> 靜態代理 : 就僅是將RealSubject包到ProxySubject裡
```java
new ProxySubject(real);
```

> 動態代理 : 透過反射

```java
class ProxyHandler implements InvocationHandler{
    private Subject subject;
    public ProxyHandler(Subject subject){
        this.subject = subject;
    }
    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        Object result = method.invoke(subject, args);
        return result;
    }
}
//建立一個該subject的handler 處理對應的invoke
handler = new ProxyHandler(real);

//建立ProxySubject 給予其該有的介面與handler 會動態產生一個class
proxy = (Subject)Proxy.newProxyInstance(
    RealSubject.class.getClassLoader(), 
    RealSubject.class.getInterfaces(), handler);
proxy.request();

```

Annotation
======
[ref](http://puremonkey2010.blogspot.tw/2013/05/java-java-reflection-annotations.html)
類似metadata 提供訊息用以描述內容 例如某個method是override的

用途
- 編譯檢查 如 @Override,@SuppressWarnings,@Deprecated
- 執行期間以反射取得某物件的Annotation 以提供相應的處理
- 編譯時搭配額外編譯工具提供自動產生代碼 如Dagger/JavaDoc

```java
@Retention(RetentionPolicy.RUNTIME) //使其能在runtime時保留 供反射使用
@Target(METHOD.TYPE)    //能被用在method上
public @interface MyAnnotation {
     String value() default "test"; //宣告需要一個字串變數
}

public class TheClass {
    @MyAnnotation("unit test")
    public void doSomething() {
    }
}

//透過反射取得其Annotation
Method method = TheClass.class.getMethod("doSomething");  
Annotation[] annotations = method.getDeclaredAnnotations();
Annotation annotation = method.getAnnotation(MyAnnotation.class);  
```

捕捉 UncaughtException
======
從Thread捕捉所有未能從try catch中捕捉的意外 替即將掛掉的程式安插一些處理方式

```java
class ExceptionHandler implements Thread.UncaughtExceptionHandler
{
    @Override
    public void uncaughtException(Thread t, Throwable e)
    {...}
}
Thread.setDefaultUncaughtExceptionHandler(new ExceptionHandler());
```

內部類
======
```java
class AllInnerClass{
    //內部類 (嵌套類)
    class InnerClass{
    }
    
    //靜態內部類
    static class StaticInnerClass{
        static Object obj = new Object();  //靜態成員的初始化 1.懶初始化 2.保證thread-safe
    }

    public Object getTaske(){
        //局部類
        class LocalClass extends Object{
        }
        return new LocalClass();
    }
    
    //匿名內部類
    Runnable anonymousInnerClassObject = new Runnable(){
        @Override public void run() {}
    };

    //Lambda
    Runnable runnbale = () -> System.out.println("running~");
}
```

HTTP
======

#### HttpURLConnection
> 較低階

#### HttpClient
> 較高階 以HttpGet或HttpPost做為請求對像

```java
HttpClient client = new DefaultHttpClient(); 
HttpGet request = new HttpGet("http://tw.yahoo.com"); 
HttpResponse response = client.execute(request);
    
HttpEntity entity = response.getEntity(); 
InputStream input = entity.getContent(); 
```

