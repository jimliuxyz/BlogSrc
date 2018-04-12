---
title: __
---

### 從web登入
彈出視窗方式
申請clientID(即webClientId)通常只需要鎖網域

### 從app登入
透過app內建的widget程式登入. (client端主要是驗證apk的signed資訊,似乎與程式碼無關.)
申請登錄clientID需要:

- app id (widget id of config.xml)
- app SHA-1

登入時若沒有加webClientId只會回傳少數文字資料,所以透過app.登入若需要進一步操作用戶資料就同時也需要web client id.

### Android Studio預設的debug.keystore設定
[參考](https://stackoverflow.com/questions/18589694/i-have-never-set-any-passwords-to-my-keystore-and-alias-so-how-are-they-created)

android開發有一組預設的~/.android/debug.keystore,密碼為'android'
(當初不知道有這樣的設定,一直嘗試一些無法關聯的調整,浪費了一兩天)

```
Keystore name: "debug.keystore"
Keystore password: "android"
Key alias: "androiddebugkey"
Key password: "android"
CN: "CN=Android Debug,O=Android,C=US"
```

### 申請OAuth Client ID
取得key的SHA1

```
//自建key (做成apk要publish才使用這種方式)
keytool -genkey -v -keystore my-key.keystore -alias myalias -keyalg RSA -keysize 2048 -validity 10000
keytool -exportcert -list -v -alias myalias -keystore my-key.keystore

//使用預設~/.android/debug.keystore (開發階段使用)
keytool -exportcert -list -v -alias androiddebugkey -keystore ~/.android/debug.keystore

//申請OAuth Client ID> Android, 貼上sha1,與config.xml的'widget id'
//https://developers.google.com/mobile/add
//https://console.developers.google.com
```

### 安裝package
```
//據說REVERSED_CLIENT_ID是給iOS看的 (之前沒reverse也能正常)
ionic cordova plugin add cordova-plugin-googleplus --variable REVERSED_CLIENT_ID=com.googleusercontent.apps.686840052494-pclnksh67f04srjmnta2tqa2m7mmroct

//REVERSED_CLIENT_ID若用686...,用android測試時可以登入
//若真reverse會???

npm install --save @ionic-native/google-plus
```

### app information

device app的oauth很依賴app提供的資訊(widget id, SHA-1).

app的資訊除了在config.xml能看到,還散佈在其他檔案,當需要修改app id或一些其他ID時要注意,最好project一開始就決定好,不然可能會被搞死.

```
config.xml
plugins/android.json
plugins/fetch.json
platforms/android/android.json
```

### app登入
[Google](https://devdactic.com/google-sign-in-ionic-firebase/)

```js
this.platform.ready().then((readySource) => {
  //不加webClientId僅能取得部份文字資料,不包含idToken
  this.googlePlus.login({
    'webClientId': '686840052494-pclnksh67f04srjmnta2tqa2m7mmroct.apps.googleusercontent.com',
    'offline': true
    })
    .then(res => {
      console.log(res)
    })
    .catch(
    err => {
      console.error(err)
    });
})
```

### npm package
- [angular-social-auth (mobile base)](https://www.npmjs.com/package/angular-social-auth)


