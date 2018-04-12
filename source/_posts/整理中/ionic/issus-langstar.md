---
title: __
---

## events.js:160 throw er; // Unhandled 'error' event
[](https://stackoverflow.com/questions/48240001/ionic-events-js160-throw-er-unhandled-error-event)
執行ionic serve出現錯誤. 聽說跟瀏覽器更新有關, 但有一份較早建立的code執行時並不會有這種錯誤
> npm install ws@3.3.2 --save-dev --save-exact

## admob沒反應 但乾淨的環境正常
[Show Banner Ads In Ionic With AdMob Free Plugin](https://pointdeveloper.com/show-banner-ads-ionic-admob-free-plugin/)
> id: ca-pub-xxxxxxxxxxx 就能正常test 但依舊無法顯示廣告

## 安裝firebase-tools

```
npm install -g firebase-tools
firebase init

firebase deploy
```

## Javascript fetch
執行fetch 3000次記憶體用量就破1G,還沒弄清問題在哪....
> 找到問題了! 問題在於開啟了瀏覽器的inspection視窗 記錄了太多資訊 與fetch無關

``` js
url = "https://translate.googleapis.com/translate_a/single?client=gtx&sl=en&tl=en&dt=t&q=test"
await fetch(url);
```

## Deeplink在aot時要用字串才行

```js

push(MyPage.name);

//lazy load 跟 aot都不會有問題
push("MyPage");

```

## Hot Code Push

```js

//產生cordova-hcp.json (主要設定更新伺服器的檔案路徑)
cordova-hcp init

//手動添加config.xml, (若從code裡主導更新,這段似乎沒什麼用途)
    <chcp>
        <config-file url="http://192.168.1.102:8100/chcp.json" />
        <auto-download enabled="false" />
        <auto-install enabled="false" />
    </chcp>

content_url : 

//欲release時產生files information
cordova-hcp build
chcp.json : 版本號 更新策略
chcp.manifest : 檔案列表 path/hash

//---以下為android apk測試編譯步驟

//build in aot mode
npm run build --prod

//build android-debug.apk 在模擬器中執行
cordova emulate android

//每次重新build code最好透過模擬器把app移除,再進行下次的模擬,否則會有些混淆的現象出現

```

更新流程(開發端):

0. compile產生www內容
1. cordova-hcp build,產生版本與file hash
2. 上傳到file server (firebase deploy)

更新流程(用戶端):

0. chcp.fetchUpdate(this.updateCallback, options); //帶入chcp.json路徑
1. 更新後會重新啟動



## service worker

透過chrome/application的offline測試會造成firebase/auth出現network error,但若直接斷掉wifi則能正常運作.

測試開發期間最好關閉service worker,避免搞混執行的版本.

## weakMap做cache失敗
做cache需要weakRef(weak value),但javascript的weakMap是weak key.

如此...要做程式內能自動釋放參考的ref就要靠自己join/leave了...使用上的繁雜度整個飆高.

## WeakMap
屬於weak key. 其方法無法取得keys也沒辦法拜訪,只能當普通的Map用,但好處是物件放進WeakMap後不會使得該物件無法被回收.

```js
console.clear();

class Cls{}
let a = new Cls();

const map = new WeakMap();
map.set(a, "a")
map.set(new Cls(), "b")

console.log(map);
setInterval(()=>{
  console.log("---");
  console.log(map);
  //b會從map中消失
}, 1000);

```
### cache兩個目的

- 加速讀取
- 集結資料,使其參考或聽取同一對象,達到自動資料同步


## await的危險瞬間

```js

class MyData{
    uid;
    data;
    init(uid){
        this.uid = uid;
        data = await getUserData(this.uid);
    }
    set(data){
        this.data = data;
        await setUserData(this.uid, this.data);
    }
}
//預設的順序是先init後再set
//但其實無法預估getUserData進入異步後,會不會有set動作進入

//最陽春的解法: ()
    init(uid){
        data = await getUserData(uid);
        this.uid = uid;
    }
//或用一些變數鎖,但寫出來都不會好看.
//或用UI鎖住畫面或按鈕等待動作結束

//最好的方式是uid是一次性唯讀的.
//但就變成不同uid不能共用於一個物件中,有時候希望一個reference從頭用到尾(重複利用),避免抽換reference時造成大範圍的連鎖反應.

data$ = ReplaySubject<T>(1);

function onSrcChange(src$){
    oldsrc$.unsubscribe();
    src$.subscript(data => {
        data$.next(data);
    });
}

//以上這個方法也有困擾,因為<T>本身可能不單是data還有method,其中的data還需要再訂閱,要分散出去也很困擾.

    
```


## firebase權限與安全性

在firebase限定權限意味著每筆私有資料不能參雜而外資料,例如某人的bookinfo中不能含有views的統計,因為views的統計是由讀者觸發寫入.

如此就需要制定更多的data table,在client做更多的join,寫入時也要同時顧慮到對其他連結table是否有影響需要一併處理

若不在firebase設定權限,就必須在client端做好安全控制,這部分是危險因子比較多的.


## Can't resolve all parameters...
```js
Uncaught Error: Can't resolve all parameters for EditorPage:
 ([object Object], ?, [object Object], [object Object], [object Object]).

readyGoHome() {
    this.navTo(HomeSlidesPage); //<--因為HomeSlidesPage引起,無論是否被執行
}
```
這似乎與webpack或即時編譯有關.

## Subject與ReplaySubject的些微差距
```js
const rp = new ReplaySubject<number>(1);
const rp = new Subject<number>();
   
let c = 0;
setInterval(async () => {
  rp.next(++c);

  const c_ = c;
  const v = await rp.take(1).toPromise();
  console.log("c:"+c_ + " v:"+v)
}, 1000);

//ReplaySubject
c:1 v:1
c:3 v:2

//Subject => v1不見了
c:1 v:2
c:2 v:3

```
主要的問題是產生在toPromise,因為promise需要"下一個值才被驅動",對一般的Subject是有順序關係的,toPromise是沒辦法取得前面next值,所以總是要等到下一輪的next. 而ReplaySubject只要曾經有值就立刻發送,沒有時序問題.

## 資料構思

需求有時候很難說,隨著細節越來越多,才會發現設計的不足,如果能一次想遠一些,對設計的時程會有很大的助益.

```js

//需求:要能分辨Quiz的ans為yes或no
Quiz:{
 quiz,
 ans
}
quizs = Quiz[];

//進階需求:要知道多少個yes與no
getCount(){
    //for loop...
}

//進階需求:資料量大時for loop不流暢
yes_cnt++;
no_cnt--;

//進階需求:要在yes group隨機取樣
quiz_yes = Quiz[];
quiz_no = Quiz[];

//進階需求:ans的yes/no可能動態改變
toggleYesNo(quiz) {
    quiz_yes.push(quiz);
    quiz_no.remove(quiz);
}

//進階需求:需要保留原quizs排序
toggleYesNo(quiz) {
    quiz_yes = quizs.filter(data=>data!quiz);
    //ref被整個取代了
}
another_ref = quiz_yes; //不知道原本的ref被取代

//...
```

## offline cache
 - cache放哪? 安全性? 被讀取被竄改?
 - data version control
  0. version
  1. dirty ? for writable offline
 - read policy
  0. by version : 比對版本後決定是否用遠端資料
  1. remote first : 有遠端資料就使用( no version)
  2. remote only : 無論如何都用遠端資料(等同沒有cache)
 - write policy


## 使用Data的考量
 - 資料的取得 (初始化/變更)
 - 資料的寫入
  - 注意資料的污染
 - 資料的用法 (顯示/運算)
 - 減少return值得依賴 改採event
  
```js
//舊思維,偏向同步動作的思考
class MyPage{
    mydata:any;
    
    constructor(){
        //mydata是否為clone data?寫入是否影響到來源資料
        mydata = db.get();
        
        db.onChange(data=>{
            mydata = data;
        });
        //以上兩個動作有些重複
    }
    
    set(value){
        //更動mydata最好不要牽動到來源資料(共用),所以最好為clone
        mydata.value = value;
        //set方式較佳,最終正確資料再從onChange返回
        db.set(mydata);
   }
}

//新思維,observable,immuteable,讀寫異步
class MyPage{
    mydata:any;

    constructor(){
        //一次處理資料的初始化與資料變動與設定後的套用結果
        //可以使用observable的op操作資料事件
        db.subscribe(data=>{
            mydata = clone(data);//如果需要變動mydata就clone一份
        });
    }
    set(value){
        //以功能性函數取代直接污染資料
        db.changeValue(value)
        .catch(...);
    }
    destory(){
        db.unsubscribe();
    }
}

```

## 惱人的資料連動


#### listA與listB都有itemC
 0. 當itemC改變或移除時如何通知A與B(完整機制的制定)?

#### itemC與itemC'
 0. 都是itemC在程式中是否要共用一個實體或各別實體?
  0. 共用實體 更新時使用方便 但要做維護單一實體的機制
  1. 各別實體 取用時方便 但維護資料同步時就很頭痛了

#### list與item
 0. list中的item是否要在data model裡就整合在一起?
 1. 或各自獨立 視各別應用 使用時再整合?

#### 正規與反正規
 - 正規 寫入方便 但讀取時要用uid連續查詢
 - 反正規 寫入時要維護多組資料 讀取時方便

#### 太過三心二意
 - 做了本地快取原是為了離線瀏覽,但又想用來減少流量,所以又做版本控制,造成混淆,與難度大增.
  0. 本地快取:只在資料無法從遠端取得時使用. 不會複寫遠端.(但要想想如何從快取中恢復到遠端,遠端永遠是對的)
  1. 本地快取且可離線編輯:更新遠端時要注意資料是否已同步,煩雜很多.
  2. 減少流量:需要版本控制,進而需要穩定安全的資料寫入.(用firebase時版本號不能用datetime,因為時區校時等因素客戶端的時間永遠不在一條線上)(就算是同一個用戶只維護自己的資料,也不能用時間當版本號)(用transaction做累加是唯一的可能性)

偶爾想要這樣偶爾想要那樣,反而不能這樣也不能那樣...設計前還是要再多想想,可以少撞一些牆.

## 資料模組

#### 應用上會面臨的排列組合

[(權限/資料安全)私有/公有/混合] [本地離線快取/遠端資料庫] [線上/離線] [離線資料回寫(安全性?)] 

[新增/刪除/讀取(JOIN)/更新(LINK)] [執行期間快取(weak map)] [(程式內資料映射)被動更新/事件更新]

- 複雜度
 - 資料新增/修改 可離線? uid? 如何驅動回寫?


本地快取 --> 網路來源 --> 應用數據 --> 複製擴散 --> CRUD --> 收斂返回

- 快取機制
 - 僅讀 (online/offline)
 - 若要寫 版本就很重要 可能需要merge機制 或更陽春 暫時可寫 但一連線仍用網路來源覆蓋
 - 快取容量控制?

- 本地與遠端 資料同步的邏輯
 - 以較新的為主(time version)(自有資料)
 - 永遠以遠端為主 (共享資料) 除非是 資料建立者

- 如果保護一連串的網路存取都正常?
 - 例如:po一篇文章,要同時將uid存到多個資料表
 - 以往這樣的動作都在server端完成,可靠性高很多. 用firebase變成server跟client的code寫在一起. [雖然都在client執行但應該分成兩個獨立的個體寫會比較好,不幸我將他們寫在了一起,變得比較混亂也難以解偶]
 - 很困難... 因為用戶隨時都會遇到1.網路不順 2.當機 等等...造成動作被中斷.
 

- 程式內存取真實資料
 - 直接存取 : 方便 但欠缺規矩 難debug 難做異常處理
 - 透過api : 較好管理 但api一多就相當困擾
 - immuteable.js : 永遠保留資料原始狀態 以這樣的概念設計 可以強迫資料維持正確性 架構上比較強壯 很難走捷徑

- 資料顯示/使用者輸入
 - 不需轉換 : 
 - 通過轉換 : 

- 資料更新 : 全部/部分 哪部分?
 - 如果資料已被刪除呢?或起初就沒有同步呢?
 - *** 本地要更新遠端時一定要確認資料同步(尤其是共享資料)！！！否則問題會層出不窮
 - 一個完整的資料更新動作被拆成很多個部分的小更新,就要承擔動作被中斷的風險,因為有些資料應用上僅完成一半造成的後果就是跳exception!. 且 如果做一半的中途被 某人讀取?後果是???

- 資料保護
 - 版本不被舊資料覆蓋 <----自有資料
 - 同一時間僅允許一人寫入(累加) <----共享資料

- 資料同步
 - 本地端與遠端永遠都存在著不同步的疑慮,問題是寫入造成的影響
  0. 這筆資料只屬於A且是A決定寫入的 : 沒問題
  1. 這筆資料只屬於A但由系統寫入 : 系統必須聰明到能否寫入
  2. 這筆資料屬於大家,不管是誰寫 : 一定要transaction.


- 資料版本控制
 - 以大小來區分新舊,必須確認這個值一定只會越來越大
 - 以變化來區分新舊,例如本地端是fw084j2,但遠端是cwo392v,遠端有變動就是新的(本地端是唯獨)
 - 以firebase來說,要做版本控制最好全程使用transaction,這是最安全的方式,但資料太大更新又太頻繁就很耗損流量,因為交換資料時還要確定自己的資料是最新的,就算資料僅供一個用戶寫入,但一個用戶操作多台機器的情況也是有的. 除非!!!資料的寫入是用戶"確認"後才寫入,就無所謂版本了.

- 離線操作
 - 難度在offline恢復到online的機制,如何無縫接軌.
 - 若是處在離線資料,編輯後要拿來更新遠端資料時,要如何處置. (難度很高最好禁止)
  0. 陽春的辦法:只要是離線資料一律不允許更新,直到user自己refresh取得遠端資料.

- 遠端資料
 - firebase一類的資料庫在共有資料的保護比較困擾,因為每個用戶的資料可能都在不同的狀態,每一個都是異步動作.(資料在遠端但保護要在本地端)
 - 自有資料 : 可以假設同一時間僅有一個用戶使用 (或一人寫/多人讀)
 - 共享資料 : 同一時間可能多方寫入 刪除 讀取 要注意本地與遠端的狀態

- firebase要transaction的欄位要禁止null(共享資料要有初值),因為null同時也被用來判別object是null. 而且transaction不適合太大的物件,因為很常需要整個物件download下來.

## CORS

[en](http://blog.ionic.io/handling-cors-issues-in-ionic/)

[cn](https://hao5743.github.io/2016/12/03/Handling-CORS-issues-in%20Ionic/)

## firebase只取某個欄位

```js
data = {
 value:3;
}
//ref("data/value")即可取到value

data = {
 uid1:{
    email="A"
    value:3;
 },
 uid2:{
    email="B"
    value:3;
 }
}
//ref("data/uid1/value")
//如果透過query {email="B"}就沒辦法單獨取到value
```

## firebase goOffline
goOffline後若對database操作會被block住!!直到goOnline繼續完成!
網路斷線也是如上的情況!

也就是說動作一旦下達就無法取消



## firebase時間排序
通常會有這樣的需求where name='jim', sort by datetime

```js
ref.orderByChild("name").equalTo("jim")
//排序欄位給name用後,沒辦法再用其他欄位排序了...
//只能用預設的order,也就是object key
//所以最初建立key時就要以時間建立
key = timeStr+randomUID

```

## tab的slide indicator在link back後被歸零
只有在{mode:'ios'}時,不設定就沒這問題
可以看到換頁動畫的時候被推到0

```js
    IonicModule.forRoot(MyApp,{mode:'ios'}),
只能改過場動畫了
    IonicModule.forRoot(MyApp,{mode:'ios',pageTransition:'md-transition'}),
```

## ion-item中放入自己的元件會被移除掉
必須在元件html上加以下屬性(directive) item-content, item-left or item-right
 
## Can't bind to 'ngClass' since it isn't a known property of ...
ngModule要import CommonModule

## function call 與 action command
```js
//message call
function action(cmd:any) {}
=> 沒必要少用這樣的寫法,雖然cmd的彈性很大,但後續很難維護

//function call
function doSomething() {}
=> 用途清楚,參數的依賴明確,有lint方邊追蹤除錯

```

## ion-slide
當用ion-slide時,ion-content必須被包到ion-slide中,否則會影響髒檢查的效率.

## 文字垂直置中的技巧
用padding取代height. 但多個block若有行數不同時就會偏移了.

## ionic url?var=123 參數部分會被自動去掉

## if(!value) 跟 if(value==null)
- !為強制轉型為boolean

```js
Boolean(0) : false <== 注意這個
Boolean(1) : true

Boolean('') : false <== 注意這個
Boolean(' ') : true

//檢查'物件'是否被設定是可以的,但用在'數字'或'字串'要謹慎
Boolean(null) : false
Boolean(undefined) : false
```

- ==null則為參考比對

```js
//各種型別都適用的檢查方式
(undefined)==null : true

(undefined)===null : false <== 注意這個
```

## database與表單
database雖然可以放boolean跟number,但database(web)回來'可能'會變string,且html from的處理也都以字串較多. 有時不小變成true跟"true"混用,會造成很多困擾,可能盡量用string會好點?

## forEach跟for的差別

- forEach是以callback執行,呼叫CB時沒有前綴await,所以在CB中遇到await就會轉成異步,不會等待.
- 所以欲同步執行就不能用forEach!!!!!

## Firebase難處
order跟where的語句只能對應一次. 一份資料很難有多種的query方式. 例如post info希望:

1. 以author排序取回
2. 限定type以views排序取回

就必須將post分別置於兩個資料表.

## Firebase怎麼規劃

就我的需求:

0. 能夠離線使用
0. 盡量保持離線(因為免費project僅有100個連線)
0. 減少流量(檢查遠端資料版本)
0. 變更DB的可能性

可能的做法:

- 做正規劃
 0. 免去需更新多個資料表的問題. (但會增加query優化的難度,使得效能變差流量變大)
 0. 資料對應比較不複雜, 映射到內部暫存資料庫會比較容易些.
 0. =>>>`無法正規劃!!!`:因為只存uid最後會面臨無法排序選取的問題
 0. =>>> 半正規劃:uid+order,可以,但可能不如直接用反正規劃,因為order欄位也需要更新的
 
## Firebase卡死

```js
//data中有一個type欄位值是undefined
let x = ref.set(data)
    .then(data => { console.log("yes"); })
    .catch(err => { console.error(err)})
console.log("???")

//沒有錯誤訊息,什麼都沒有
//delete data.type; 或 data.type=0;就過去了
```

## Firebase的update
```js
data:{
    x:9,
    y:7,
    more:{
        a:5,
        b:3,
    }
}

//錯誤用法 : data下的more會整個被{a:0}取代
ref = "data"; update = {more:{a:0}}

//正確用法 : data/more下的a被0取代
ref = "data/more"; update = {a:0}

//跟我預期的不一樣
//如果要同時更新x跟a必須update兩次 或 整個data重新set一次
//故若有多層的物件要更新就要多一道手續
```



## Firebase的transaction
transaction的callback至少會被呼叫一次

#### 新增資料
```js
ref.transaction(
  (currentData) => {
    if (!currentData) {
      //＊.遠端有無資料都會進入 (第一次都會丟null,目的應在先取得欲更新的資料,而非先從遠端下載資料)
      //1.遠端若無資料,此次return將新增到遠端
      //2.遠端若有資料,又與此次return的資料不同時,下次就會丟回遠端資料
      return newData;
    } else {
      //當前次return與遠端資料不相等時,此次會丟回確切的遠端資料,以供更新(或原物返回).
      return currentData;
    }
  }
)
```

#### 修改資料
```js
ref.transaction(
  (currentData) => {
    if (!currentData) {
      return currentData;
    } else {
      return currentData.count++;
    }
  }
)
```


## angular偵測dirty失敗?

```js
value = 0;
testFun(){
    this.value = 99;
}

//之後用以下三種方式改value的值,其中case1無法被angular偵測其值被修改

//case1
setTimeout(this.testFun, 0);

//case2
setTimeout(() => {
    this.testFun();
}, 0);

//case3
setTimeout(() => {
    this.test = 99;
}, 0);


```

答案 : testFun為一般function的宣告方式,其this為event caller. 完全是因為this指向了不預期的物件.

```js
setTimeout(this.testFun.bind(this), 0);
```

## firebase的orderByChild()無效?
firebase伺服器端會依照order排序並取出結果(例如從10筆資料取出5筆最小的),但其取出的結果不會被排序,主因是firebase是key:value的db,儲存array會被轉成key:value物件,所以回傳也是key:value的物件,要排序只能自己轉array再sort.

## ionic一個ts裡放兩個component,分別有兩個templateURL會造成webpack掛掉
可能立即掛掉 或 再次ionic serve時掛掉

## ionic的lifecycle hook有點詭異?
無法確認是我理解錯誤 還是 真的有錯 很多hook都不會被call





