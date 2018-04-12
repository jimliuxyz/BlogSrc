---
title: DB-MySQL
date: 2018-04-04 16:30:37
tags: Android
---

## SQLiteOpenHelper
[ref](https://www.jianshu.com/p/8e3f294e2828)

```java
public class DBHelper extends SQLiteOpenHelper {
    public DBHelper(Context context) {
        super(context, "mydb.db", null, 0);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL("CREATE TABLE IF NOT EXISTS user(\
            _id INTEGER PRIMARY KEY AUTOINCREMENT, \
            name TEXT)"
        );
    }
}

dbHelper = new DatabaseHelper(context);

SQLiteDatabase db = dbHelper.getWritableDatabase();
SQLiteDatabase db = dbHelper.getReadableDatabase();
db.close();

//insert
ContentValues values = new ContentValues();

values.put("id", 1);
values.put("name", "jim");
db.insert("user", null, values);
db.execSQL("insert into user (id,name) values (1,'carson')") 

//update
values.put("name", "jim2");
db.update("user", values, "_id=?", new String[] { "1" });
db.execSQL("update [user] set name = 'jim2' where _id=1");

//delete
db.delete("user", "_id=?", new String[]{"1"});
db.execSQL("delete from user where _id=1");

//selete
Cursor c = db.rawQuery("select * from user where id=?",new Stirng[]{"1"});
c = db.rawQuery("SELECT _id, name FROM user");
c = db.query("user", new String[] { "_id", "name" }, "_id=?", 
        new String[] { "1" }, null, null, null);
```

#### Cursor
```java
//遍歷1
while (c.moveToNext()) {
    int idx = c.getColumnIndex("_id"); //取得欄位idx
    int id = c.getInt(idx); //取得資料
}  

//遍歷2
c.moveToFirst();  
while(!c.isAfterLast()){  
    //dosomething  
    c.moveToNext();  
}

//記得關閉
c.close();
```

#### 將db檔預先置入apk
> 將寫好的db放到res/raw下 在開啟db前將資源寫入android檔案系統

```java
InputStream myInput = context.getResources().openRawResource(R.raw.database);

File file = context.getDatabasePath("mydb.db");

if (!file.exists()) {
  try {
      OutputStream myOutput = new FileOutputStream(file);

      byte[] buffer = new byte[1024];
      int length;
      while ((length = myInput.read(buffer))>0){
          myOutput.write(buffer, 0, length);
      }
      myOutput.close();
      myInput.close();
      mIsDBOK = true;
  } catch (Exception e) {
      e.printStackTrace();
  }
}
```