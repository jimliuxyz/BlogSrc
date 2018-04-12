---
title: 功能測試
date: 2018-04-04 15:51:42
tags: Android
---

## JUnit
> 僅於本地JVM上測試 又稱單元測試(函數功能測試) 不依靠android環境

> 以滑鼠右鍵選單"Go To>Test"快速建立test class

#### Annotation
- @Test 註解一函數為 測試使用
- @Test(expected = Exception.class) 使符合預期時拋出異常
- @Test(timeout=100) 指定測試需在100ms內完成
- @Before 註解一函數為 每個測試函數執行要先執行的函數
- @After 邏輯同上
- @BeforeClass @AfterClass 邏輯同上 但僅為整個class執行一次
- @Mock 宣告一個`假物件`
- @Captor 宣告一個`叫用參數捕捉器`(以取得叫用參數)
- @Spy 監視一個真物件
- @Rule 修飾TestRule

#### Matchers
> 比對器 類似過濾行為 能以廣義的形容區別物件

```xml
eq
is 是某個object
isA 是某個class
not
anyInt
hasItem
```

#### Assert
> 斷言結果 `判斷值是否符合預期` 若不符合斷言即為測試失敗

```java
//斷言結果需為true
assertTrue("test failure", mockins.func() == 1);

//斷言instance.func()執行回傳為1
assertThat("test failure", mockins.func(), is(1)); //用Matcher判斷
assertThat(mockins.getList(), not(hasItem(not_expected));

//assertEquals
```

#### mockito 製作假物件
[ref](https://blog.csdn.net/shensky711/article/details/52771493)
> 通過製作假物件與指定其反應回傳 來驗證該假物件`是否被呼叫?以何參數呼叫?`

> 通常被測物需為真物件(因為要測試他的邏輯) 其依賴物為假物件(不著重邏輯是否正確或回傳什麼 僅著重是否被真物件正確叫用)

```java
//1. 產生mock物件
@Mock MyClass mockIns; //不需要new
MockitoAnnotations.initMocks(this); //但之後要做init使class內的@Mock生效

//1. 產生mock物件
LinkedList mockList = mock(LinkedList.class);
LinkedList mockList = mock(LinkedList.class, Mockito.RETURNS_SMART_NULLS); //指定預設的回傳

//2. 指定mock物件的反應 (此動作稱為Stubbing)
when(mockList.get(0)).thenReturn("it's a mock string");
when(mockList.get(1)).thenThrow(new RuntimeException());
when(mockList.get(anyInt())).thenReturn("element");
when(mockList.size()).thenReturn(1, 2, 3); //每次回傳都不同
//doReturn, doThrow, doAnswer, doNothing, doCallRealMethod
doThrow(new RuntimeException()).when(mockList).clear();
reset(mockList); //清除所有Stubbing

//3. 經過某些動作操作後...即可進入驗證
mockedList.add("once");

//驗證1. 驗證mock物件的方法是否被呼叫(times, atLeast, atMost, atLeastOnce, never, timeout)
verify(mockedList).add("once");   
verify(mockedList, times(1)).add("once"); 
verify(mockedList).set(anyInt(), eq("element")); //用Matchers便於廣義的驗證

//驗證2. 連帶驗證呼叫順序
InOrder inOrder = inOrder(mockedList);
inOrder.verify(mockedList).add("first");
inOrder.verify(mockedList).add("second");

//驗證3. 捕捉呼叫參數
ArgumentCaptor<String> args = ArgumentCaptor.forClass(String.class);
mockedList.add("John");
verify(mockedList).add(args.capture()); //驗證add的叫用 同時捕捉叫用的參數
assertEquals("John", args.getValue());
    
```

#### spy監視真物件

```java
List list = new LinkedList();
List spy = spy(list);
//之後spy即類似mockito的操作
```

#### TestRule
> 能在測試函數前後插入動作 例如偵測測試函數的執行時間

> 加入TestRule時需用@Rule註解才能與JUnit掛鉤

```java
public class MyRule implements TestRule {
    @Override
    public Statement apply(final Statement base, final Description description) {
        //base : 一個能夠驅使測試函數被執行的物件
        //description : 一個相關於測試函數環境的描述
        return new Statement() {
            @Override
            public void evaluate() throws Throwable {
                //即測試前
                base.evaluate();
                //即測試後
            }
        };
    }
}

@Rule
MyRule rule = new MyRule()
```

## Espresso
[Android testing samples](https://github.com/googlesamples/android-testing)
> 依賴於JUnit的一個測試框架

> UI測試 依靠android環境執行 針對view元件進行驗證

#### 基本三步驟
- 取得view或data的互動 onView / onData
- 執行動作 perform
- 檢查驗證 check

#### 大致動作
```java
//操作view
onView(withId(R.id.fab_add_task)).perform(click());
//檢查view
onView(withId(R.id.add_task_title)).check(matches(isDisplayed()));
```

#### onView
> ViewInteraction onView(Matcher<View> viewMatcher)

> 透過matcher取得view的控制

```java
//Matcher<View>
allOf 合併多個matcher
withId
withText
isDisplayed

//其父view的id為R.id.recyclerView
isDescendantOfA(withId(R.id.recyclerView))

//其父view為ListView.class
isDescendantOfA(isAssignableFrom(ListView.class))

hasDescendant
hasSibling
```

#### perform
> ViewInteraction perform(final ViewAction... viewActions)

> 執行動作

```java
//ViewAction
click()
LongClick()
clearText()
replaceText("")
typeText("")
closeSoftKeyboard()

//專為RecyclerView設計(actionOnItem)
onView(allOf(withId(R.id.recyclerView)))
    .perform(actionOnItem(hasDescendant(withText("title")), click()));
```
#### check
> ViewInteraction check(final ViewAssertion viewAssert)

> 斷言

```java
//ViewAssertion
matches(isDisplayed()) //使用view matcher來斷言
doesNotExist()
isChecked()
```

#### 其他
```java
pressBack() //按下返回鍵
TestUtils.rotateXXX() //控制手機旋轉
TestUtils.getCurrentActivity()

```
#### TestUtils
> 一個工具類 提供靜態方法控制手機裝置 rotate / getCurrentActivity

#### 自訂Matcher
```java
//一個回傳Matcher的function
private Matcher<View> withItemText(final String itemText) {

    return new TypeSafeMatcher<View>() {
        @Override
        public boolean matchesSafely(View item) {
            return allOf(
                    //其父view為ListView.class
                    isDescendantOfA(isAssignableFrom(ListView.class)),
                    //且text為itemText
                    withText(itemText)
                ).matches(item);
        }

        @Override
        public void describeTo(Description description) {
            description.appendText("is isDescendantOfA LV with text " + itemText);
        }
    };
    
}
```

