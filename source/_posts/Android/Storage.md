---
title: Storage
date: 2018-04-04 18:37:14
tags: Android
---

## Storage

> internal與external是以app間分享的角度,external表示可被分享

> 無論internal或external 在app移除都會跟著移除 除非用是PublicDirectory

#### internal
> /data/data/ `被連結到(/data/user/...)`

> 一定存在 別的app不能訪問 用Context.filesDir取得

#### primary external
> /Android/data/PACKAGE_NAME `被連結到(/storage/emulated/...)`

> 可能是`內部記憶體卡的分割`或`外部記憶裝置(Adoptable Storage)` 用Context.getExternalFilesDir取得

#### secondary external
> 外部記憶裝置 不一定存在

```java
//Context.filesDir (/data/data的連結)
    /data/user/0/PACKAGENAME/files

//Context.getExternalFilesDir
    /storage/emulated/0/Android/data/PACKAGENAME/files/Pictures

//Context.getExternalFilesDirs (Primary/Secondary)
    /storage/emulated/0/Android/data/PACKAGENAME/files/Pictures
    /storage/12E9-1105/Android/data/PACKAGENAME/files/Pictures
```

```java
//Environment.getExternalStorageDirectory
    /storage/emulated/0
    
//Environment.getExternalStoragePublicDirectory
    /storage/emulated/0/Pictures
    
//Environment.getDataDirectory
    /data
//Environment.getDownloadCacheDirectory
    /cache
//Environment.getRootDirectory
    /system
//Environment.getExternalStorageState
    mounted
```
