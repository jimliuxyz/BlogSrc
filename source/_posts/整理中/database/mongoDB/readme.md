---
title: __
---

http://ithelp.ithome.com.tw/articles/10165786
http://mongodbcanred.blogspot.tw/2015/01/mongodb.html
http://mongodbcanred.blogspot.tw/2015/01/mongodbinsert-document-remove-document.html
http://ithelp.ithome.com.tw/articles/10166066?sc=pt

---

RDBMS到NoSQL

DB->DB
TABLE->COLLECTION : 可以不用事先定義欄位 (稱為Schema-Free)
RECORD/ROW->DOCUMENT : COLLECTION下的每筆DOC允許有不等數量的欄位
Column->Field
PK->_id


DOC為JSON格式(儲存的格式為BSON,即binary)

可透過 subcollections來組織資料庫,例如IT部門下有兩個子部門ERP跟MIS,就可以分別建成三個collection
IT / IT.ERP / IT.MIS

---

選取或建立DB
use mydb

顯示目前操作的DB(db相當於指向目前使用的DB)
db

顯示所有DB(至少含有一個collection的DB才會被顯示)
show dbs

刪除DB
db.dropDatabase()

---

建立COLLECTION
db.createCollection(NAME, OPTIONS)

(直接用insert也可以用預設的OPTIONS建立COLLECTION)

db.createCollection("mycol")
db.mycol.drop()
show collections

---

插入DOC
```js
db.collection.insert(<documents>,<option>)

db.mycol.insert({
   title: 'MongoDB Overview', 
   description: 'MongoDB is no sql database',
   tags: ['mongodb', 'database', 'NoSQL'],
   likes: 100
})
```
<option> : 資料寫入確保
writeConcern:寫入確保等級設定
               w:-1     (嚴謹性：非常低)；發生資料庫寫入錯誤一律不回傳
               w:0      (嚴謹性：低)       ；只可以偵測到網路錯誤
               w:1      (嚴謹性：中 預設等級)
               w:1,j:1  (嚴謹性：高)       ；在寫入日誌後才回傳處理過程。
               w:2      (嚴謹性： 高 )     ；這個級別只在replica set的部署模式下生效

db.collection.insert({name:'jim'},{writeConcern:{w:1,j:1}})

---

更新DOC (update:僅更新值) db.COLLECTION_NAME.update(WHERE, UPDATED_DATA, OPTIONS)
```js
db.mycol.update({'title':'MongoDB Overview'},{$set:{'title':'New MongoDB Tutorial'}},{multi:true})
```
預設僅能更新單一doc

$set : 設定key的value
$unset : 刪除key
$inc : 累加


更新DOC (save:有_id時為'取代'doc,沒_id時相當於insert)

刪除DOC
```js
db.COLLECTION_NAME.remove(WHERE,justOne<option>)
db.mycol.remove() //刪除全部
```

---

查詢DOC (find/findOne)
```js
db.COLLECTION_NAME.find(WHERE).pretty()
```

用pretty()返回格式化內容

WHERE:
where A = 'ABC' : {"A":"ABC"}
where A < 1 : {"A":{$lt:1}}
lt lte
gt gte
ne
$in   (包含)
$nin (不包含)
$and   (和)
$or   (或)
$not (否)
$all 匹配所有指定內容才行(and) (key為陣列時)
$in 匹配一筆指定內容即可(or) (key為陣列時)
$size (node數量) (key為陣列時)
$type 匹配資料型態
$elemMatch 進入sub doc匹配
$slice 取陣列的部分元素 (稍微複雜,要用例子驗證一下行為)


OR: (預設為AND)
A=1 and (B=2 or C=3)
```js
{{"A":1},{$or:[{"B":2},{"C":3}]}}
```


key:value區分大小寫,可以用正規表示法(但速度比較慢)
db.mycol.find({name:/Russell/i})

---

投影 (Projection) : 僅查詢DOC的部分資料 (1:顯示 0:不顯示)(_id預設為顯示)
```js
db.mycol.find({},{"title":1,_id:0})
```

限制DOC數量
```js
db.mycol.find({},{"title":1,_id:0}).limit(2)
```

略過N個DOC
```js
db.mycol.find({},{"title":1,_id:0}).skip(1)
```

取得DOC數量
```js
db.mycol.find().count()
```

---

Indexing

---

Aggregation and Groups
