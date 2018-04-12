---
title: ContentProvider
date: 2018-04-04 15:49:49
tags: Android
---


## Loader
> Fetch local data from disk using a Loader on a background thread

> Propagate data changes through a Loader to the UI

[ref](https://blog.csdn.net/axi295309066/article/details/52536960)
[video](https://www.youtube.com/watch?v=3d9BeWqlfTk)
> 簡單理解為一個監聽器介面機制 很主要目的在實現LiveData(即時資料顯現)(ContentObserver)

> Activity重建時LoaderManager中已建立的Loader不需重建 能透過id續用


#### AsyncTaskLoader (abstract extends Loader)
> 分離IO與UI使用的執行緒

> 因為ContentProvider執行的thread是caller提供的 透過AsyncTaskLoader能讓caller處於非main thread 避免影響ui

#### CursorLoader (extends AsyncTaskLoader)
> 建立cursor並透過cursor監聽uri是否被通知為變更 若是則Loader會叫用callback

#### 使用loader
```kotlin
//實作介面
this implements LoaderManager.LoaderCallbacks<Cursor>

override fun onCreateLoader(id: Int, args: Bundle?): Loader<Cursor> {
    //要求建立cursor (語句與用contentResolver.query建立時相同)
    return CursorLoader(this, URIContract.MyEntry.URI, arrayOf("_id", "value", "time"),
            null, null, null)
}

override fun onLoadFinished(loader: Loader<Cursor>?, data: Cursor?) {
    //loader建立完成 (cursor設定監聽的uri)
    data?.setNotificationUri(contentResolver, URIContract.MyEntry.URI)
    adapter!!.swapCursor(data)
}

override fun onLoaderReset(loader: Loader<Cursor>?) {
    //loader需被結束(釋放資源)
    adapter!!.swapCursor(null)
}

//建立CursorAdapter
adapter = SimpleCursorAdapter(this, android.R.layout.simple_list_item_1, null,
    arrayOf("value"), intArrayOf(android.R.id.text1), 0)

var lv = findViewById<ListView>(R.id.lv)
lv.adapter = adapter

//啟動loader (啟動後的實體manager自己會管理)
loaderManager.initLoader(0, null, this)

```

#### 自訂Loader

```java
public class StringLoader extends AsyncTaskLoader<String> {

    private String mCache;
    public StringLoader(Context context) {
        super(context);
    }

    @Override
    protected void onStartLoading() {
        if (mCache != null) {
            //跳過loadInBackground
            super.deliverResult(mCache);
        } else {
            //驅動loadInBackground與deliverResult達到資料更新
            forceLoad();
        }
    }
    @Override public void deliverResult(String data){
        mCache = data
        super.deliverResult()
    }

    @Override
    public String loadInBackground() {
        return "data";
    }
    
    @Override
    public void onReset(){
        //結束該loader 釋放資源
        super.onReset()
    }

}
```

## ContentProvider
[ref](https://www.jianshu.com/p/ea8bc4aaf057)
[ref](https://blog.csdn.net/yhaolpz/article/details/51304345)


#### 行為

- 提供資料存取的獨立個體 應該是被動的存在 有權限的約束
- Activity靠ContentResovler與ContentProvider聯繫 以uri指定資源的路徑
- 回傳Cursor給外部使用

#### MINE type
> 即 `類型`/`子類型` (`應用類型`/`文件類型`) 例如 text/html

> ContentProvider的`應用類型`固定為cursor `文件類型`可以自訂

```java
"vnd.android.cursor.dir/multi"      //multiple record
"vnd.android.cursor.item/single"    //single record
```

#### uri
![](https://upload-images.jianshu.io/upload_images/1362430-b39bc91ec8e272af.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/304)

#### ContentUris
> URI解析工具

```java
//建立uri
Uri uri = Uri.parse("content://com.jxyz.provider/user") 

//添加id
uri = ContentUris.withAppendedId(uri, 5);  

//取出id
long personid = ContentUris.parseId(uri);
```
#### UriMatcher
> URI比對工具

```java
matcher = new UriMatcher(NO_MATCH); //不匹配時回傳NO_MATCH
matcher.addURI("com.jxyz.provider", "user", USER); //匹配回傳URI_USER
matcher.addURI("...", "user/#", USER_ID);   //# : int
matcher.addURI("...", "user/info/*", USER_NAME);    //* : string

switch(matcher.match(uri)){
    case URI_USER:   
        ...
}
```

#### ContentObserver
> 監視uri對應內容的變化

```java
//監聽uri變動
contentResolver.registerContentObserver(uri, true, mContentObserver)

//通知uri變動
contentResolver.notifyChange(uri, null);

//要求cursor監聽uri (使CursorLoader能自動更新)
cursor.setNotificationUri(context.contentResolver, uri)
```

#### 權限

```xml
//宣告本app自訂的權限
<permission android:name="com.jxyz.provider.READ" android:protectionLevel="normal"/>

//provider就可用該權限指定需要受約束的元件
<provider
android:name=".MyProvider"
android:authorities="com.jxyz.provider"
android:readPermission="com.jxyz.provider.READ"
android:exported="true">
</provider>

//其他app若要有該權限 則需如下宣告讓user同意該使用權限
<uses-permission android:name="com.jxyz.provider.READ"/>
```

```java
public class MyProvider extends ContentProvider {

    private Context mContext;
    DBHelper mDbHelper = null;
    SQLiteDatabase db = null;

    //Application.instance.packageName() + ".provider"
    public static final String AUTOHORITY = "com.jxyz.provider";

    public static final int USER_CODE = 1;
    public static final int USERID_CODE = 1;

    private static final UriMatcher mMatcher;
    static{
        mMatcher = new UriMatcher(UriMatcher.NO_MATCH);
        mMatcher.addURI(AUTOHORITY,"user", USER_CODE);
        mMatcher.addURI(AUTOHORITY,"user/#", USERID_CODE);
    }

    @Override
    public boolean onCreate() {
        mContext = getContext();

        mDbHelper = new DBHelper(mContext);
        db = mDbHelper.getWritableDatabase();

        //init mock
        db.execSQL("delete from user");
        db.execSQL("insert into user values(1,'Carson');");
        db.execSQL("insert into user values(2,'Kobe');");
        return true;
    }

    @Override
    public Uri insert(Uri uri, ContentValues values) {
        db.insert("user", null, values);
        mContext.getContentResolver().notifyChange(uri, null);
        return uri;
    }

    @Override
    public Cursor query(Uri uri, String[] projection, String selection,
                        String[] selectionArgs, String sortOrder) {
        int code = mMatcher.match(uri);
        if (code == USERID_CODE){
            long userid = ContentUris.parseId(uri);
        }

        //後續資料如需傳遞到ListView中 需有_id欄位
        return db.query(table,projection,selection,selectionArgs,null,null,sortOrder,null);
    }

    @Override
    public String getType(Uri uri) {
        switch(mMatcher.match(uri)){
            case USER_CODE:   
                return "vnd.android.cursor.dir/multi";
            case USERID_CODE:   
                return "vnd.android.cursor.item/single";
        }
        return null;    //封閉的app也可以直接回null
    }
}
```

#### 訪問ContentProvider
```java
ContentResolver resolver = getContentResolver();

//insert
ContentValues values = new ContentValues();
    values.put("_id", 5);
    values.put("name", "allen");
resolver.insert(uri, values);

//query
Cursor cursor = resolver.query(uri_user, new String[]{"_id","name"}, null, null, null);

//update
rui = Uri.parse(uri + "/" + "7");
int result = resolver.update(uri, values, null, null);
```


