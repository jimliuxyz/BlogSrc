---
title: Dagger
date: 2018-04-06 12:11:26
tags: Android
---

## gradle設定

```
dependencies {
    kapt 'com.google.dagger:dagger-compiler:2.5'
    compile 'com.google.dagger:dagger:2.5'
}
```

## android/kotlin注意事項

```kotlin
@Inject/@Named 註解欄位時要加field:
fragment要繼承DaggerFragment (否則從FragmemtManger取回時不會注入)
activity要繼承DaggerAppCompatActivity

```

## 示例
欲在MainActivity中自動注入Car實例

```kotlin
class Car{}

class MainActivity(){
    lateinit var car: Car
    init{
        car = Car() //希望透過別的方式將Car注入到MainActivity的car
    }
}
```

## 對象名詞定義
- dependency : 依賴物 如Car
- client : 接收依賴者 如MainActivity


## 先設想一個注入器要有哪些提示才能完善

- 依據什麼對應D和C? 用類型,但要切確,例如不能把Object注入到String field
- 怎麼知道哪個field需要被注入? 用@Inject標記field
- 哪些類是可以拿來注入的? 用@Inject標記constructor
- 建構依賴時可能需要建構參數或靈活性? 用@Provides做method回傳建構好的實體
- 所以依賴可用@Inject標記constructor"或"直接做@Provides method回傳依賴物
- 同一個類但對應多個不同的子類或不同建構參數? 用@Qualifier給各自一個別名
- field想宣告成界面類型要怎麼做? 用@Binds做綁定該類與其介面
- 注入的東西生命週期為何? 用@Scope定義
- 可以需要一個或多個生產依賴的工廠 稱作Module
- 至少要有個注射器建構這些工程吧 Component就是注射器 而Module依附在注射器裡
- 注射器下還有子注射器區別不同的環境 叫做SubComponent
- 那不就要寫很多code? 幾乎只要用interface或abstract進行描述 dagger會自動產生實做好的類別(Component/Module)

## 尚未驗證
- 依賴的可視性應該取決於注射器與子注射器的繼承關係 子能見父 但子與子間互不可見 "父可能也不能見子" (父子指的是注射器產生出的實體)

- 子的注射物無法override父的注射物 仍要用key

- 是否單例取決於scope 同名scope在不同注射器上應如同不scope 同注射器同scope間僅能有一個實體 沒指名scope表示沒此注射都用不同實體 一個依賴只能有一個scope

- @Singleton是一個Application層的scope 相當於global/root/最頂層

- 開始於一個該scope的頂層物件 該物件在產生其他依賴 這一群的依賴中

- 同一個Lazy只會回傳一個實體 與singleton無關 只是快取


## step1 @Inject
> 宣告注入物 : 在注入物的Constructor標註@Inject (一種可以不在Module中宣告的方式)

> 使用注入物 : 在其使用處(注入處)標註@Inject (可用Lazy取得緩衝物件)

```kotlin
@Singleton
class Car @Inject constructor() {}

class MainActivity(){
    lateinit var car: Car
        @Inject set
}
```
> 發現在android的dagger2, Constructor不一定需要@Inject

## step2 @Module
> Module如同依賴物的生產工廠 當有需要依賴時dagger會自己叫用這裡的fun

> Module可為'class一般類'或'abstract class抽象類'或'object實例' 其中只有抽象類能用@Binds(因為code是自動產生的,僅需提供介面)

- 用抽象函數提供依賴 其輸入參數最終要能得到實例 (靠@Inject或@Provides)
- @Binds 用抽象函數提供轉型 @Binds abstract OutCls trans(InCls obj)
- @Provides 直接提供注入函數 回傳實例

```kotlin
@Module
class CarModule{
    @Singleton  //標示provide一個singleton實例
    @Provides   //標示該fun為一provide函數
    fun provideCar() = Car()
}
```

## 一個物件要被提供給注射器
1. @I : 直接在該物件的constructor宣告@Inject (會自動產生依賴函數於注射器)
2. @P : 用@Provides寫函數回傳提供 (寫在Module裡,就可以省略1)

3. @B : 轉型需求 僅是一個輔助 1或2再加上@Binds abstract fun...

## step3 @Component
> Component稱為注入器更貼切 只需定義其介面 dagger2的builder會實作該介面 並建出前綴為Dagger的class 並用該class建立注射器實體

```kotlin
@Singleton //如果Module中有Singleton這裡也要有(原因可能跟@Scope綁定作用域相似)
@Component(modules = arrayOf(CarModule::class)) //定義注入器能注入的Module
interface  MainActivityComponent {
    fun injectCar(main: MainActivity)
}
//dagger2會自動建出DaggerMainActivityComponent並且實作injectCar()
```

## step4 實際注入
> dagger2負責建立依賴 但依賴注入的時間點仍須自己處理

```kotlin
//方式1 自有注射器 (或繼承DaggerAppCompatActivity 他能替我們注入activity的依賴)
class MainActivity{
    lateinit var car: Car
        @Inject set

    init{
        DaggerMainActivityComponent //dagger自己產生的class
            .builder()
            .carModule(CarModule()) //將Module提供給注入器
            .build()            //建立注入器實體
            .injectCar(this)    //替this注入依賴 (沒有lazy)
    }
}
```

```kotlin
//方式2 建立Android Framwork App共用的注射器 App要註冊到manifest
class App : Application() { //android.app.Application
    val injector: AppComponent by lazy {
        DaggerMainActivityComponent
                .builder()
                .carModule(CarModule())
                .build()
    }
}
```

## more1 @Qualifier 或@Named("type")
> 用來替@Inject注入處綁定@Provide fun, 解決Car能由多個provide fun產出的情形

首先要自訂Annotation

```java
@Qualifier
@Documented
@Retention(RUNTIME)
public @interface ProvideID {
  String value() default "";
}

@QualifierA
@Provides fun provideCarA():Car{...}

@QualifierB
@Provides fun provideCarB():Car{...}
```

```kotlin
//kotlin中明確指出annotation作用的位置為field:
@Inject
@field:Named("local")
lateinit var ....
或
@field:[Inject Named("local")]
lateinit var ....
```
將該Annotation註釋到@Provide與@Inject的注入處即可


## more2 @Scope
[ref](https://www.jianshu.com/p/c4ed826c1473)
> 用來綁定@Provide與@Component注入器間的生命週期 讓provide產生的實例沿用直至注入器結束

- 一個component中僅能有一個scope
- 同一個component中不允許兩個同類型的'注射物類型' (以類型的字面定義區分 無論繼承關係)
- 同一個scope中不會建立兩個同類型的'注射物實體' (即局部單例)

> 用@I或@P方式建立依賴物(當兩個都有建立時,依賴是優先從@P方式取得)

> 若是依賴在注射器中是從@P方式取得 則@I的scope宣告會被忽略 故scope應該建立於provider函數上 (很容易理解 因為provider函數的回傳是我們自己實例化的 scope限制的是該函數 而不是函數的回傳)

> @B因為是自動產生provider函數 所以會參考@I的scope宣告

## more2 @Singelton
> 即@Scope做出來的一個Annotaion 字面上的意思是Singelton 實際上每個scope中的每個類型都僅能有一個實體對映 若要對映多個 1.用@Qualifier區分 2.用@Binds轉成不同型別(非@Binds真正的目的)


## more3 @Reusable
> 類似@Singelton但不再需要與注入器綁定 multi-thread時可能會出現多個實例

## more4 @BindsInstance
> 與@Component注射器的builder相關 貌似能從builder()後接上需要提供的依賴物

## more5 注射器間的依賴
> 一個注射器的依賴來源可以是Module 也可以是另一個注射器

```kotlin
@Component(dependencies = MainActivityComponent.class) //依賴另一個注射器
interface  Activity2Component {
    fun injectCar(aty: Activity2)
}

Activity2Component aty2Component = Dagger Activity2Component.builder()
    .mainActivityComponent(mainAtyComponent) //build時需提供注射器的依賴
    .build();
```

## more6 注入器間的繼承 @SubComponent
> Annotation使用上如同@Component 同一個Component下的SubComponent可以有各自的scope 概念就跟類別繼承一樣


