---
title: Room
date: 2018-04-04 16:31:06
tags: Android
---

## TypeConverter
> 建立物件與database primitive type的轉換

```kotlin
data class Word(
        @SerializedName("naText")
        var naText: String,
        @SerializedName("taText")
        var taText: String
)

//透過TypeConverter建立object與json字串的轉換
class WordConverter {
    @TypeConverter
    fun word2Json(words: ArrayList<Word>): String {
        val json = Gson().toJson(words)
        return json
    }

    @TypeConverter
    fun json2Word(json: String): ArrayList<Word> {
        val turnsType = object : TypeToken<ArrayList<Word>>() {}.type
        val list = Gson().fromJson<ArrayList<Word>>(json, turnsType)
        return list
    }
}

@Entity(foreignKeys = [ForeignKey(entity = BookInfo::class,
        parentColumns = ["id"],
        childColumns = ["bookId"],
        onDelete = CASCADE)])
@TypeConverters(WordConverter::class) //加入需要的TypeConverter
data class BookContent(@PrimaryKey(autoGenerate = true) var id: Long,
                       var bookId: Long,
                       var words: ArrayList<Word> = ArrayList<Word>()
)
```

## ForeignKey (外鍵)
> 使該table(child)與外界的table(parent)連動 例如一起被刪除

## Embedded
> 在table中坎入其他table或pojo class 避免宣告裡必須攤平所有欄位失去結構 可作為查詢時暫用的表

> 實際的解析上有點像是將Embedded表的欄位名(變數名)平面化 所以各個表中的欄位名還不能重複 若重複還要加prefix將整個表的欄位名加前綴

> select語句針對prefix的部分必須一一列出(太麻煩了)

```sql
SELECT *, tbl_b.id as prefix_id FROM tbl_a INNER JOIN tbl_b ON tbl_a.id=tbl_b.id WHERE ...
```

## Relation
> 可以建立一個連動的查詢 整合到一個table中 (用起來方便但要多建一個class)

```kotlin
@Entity
data class BookSet(
        @Embedded
        var info: BookInfo?=null,

//BookContent.bookId對映BookInfo.id
//查詢的結果必須是List或Set
        @Relation(parentColumn = "id",
                entityColumn = "bookId") var content: List<BookContent>?=null
)
```

## Migration 升級

