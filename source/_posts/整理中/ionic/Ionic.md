---
title: __
---

### release APK
[參考](http://xyz.cinc.biz/2013/06/android-app.html)

``` js
// 產生private key (alias_name可自訂)
keytool -genkey -v -keystore mykey.keystore -alias alias_name -keyalg RSA -keysize 2048 -validity 10000

// 檢視private key
keytool -list -v -keystore mykey.keystore

```

``` js
cd ~/.android/

//for langstar / pw:9xxxxx
keytool -genkey -v -keystore langstar.keystore -alias langstar -keyalg RSA -keysize 2048 -validity 10000

keytool -exportcert -list -v -keystore langstar.keystore -alias langstar

```

```js
//自建build.json
{
  "android": {
    "release": {
      "keystore": "android.keystore",
      "storePassword": "storepassword",
      "alias": "mykey",
      "password" : "password",
      "keystoreType": ""
    }
  }
}

```

### IonicPage的問題
```js
//讓url能正常運作的兩種方式
nav.push('pagename');
<ion-item navPush="pagename">link</ion-item>
```
page的載入由ionic背景執行,在其載入之前只有透過ionViewCanEnter回傳promise阻止,用以檢查異步的custom init ready狀態.

### 編譯環境
```
//.bash_profile
//jdk must <= 1.8
export JAVA_HOME=`/usr/libexec/java_home -v 1.8`

export SDK_HOME=/Users/jimliu/Library/Android/sdk
export ANDROID_HOME=/Users/jimliu/Library/Android/sdk

export PATH=$JAVA_HOME/bin:${SDK_HOME}/tools/bin:${SDK_HOME}/platform-tools:$PATH

```


```
source .bash_profile

//install sdk dependence
sdkmanager "system-images;android-25;google_apis;x86"
//create virtual device
avdmanager create avd -n mydevice -k "system-images;android-25;google_apis;x86"

cordova platform add android
cordova platform add android@6.2.3 --save

//run on local browser
ionic serve

//run on avd
ionic cordova run android --livereload

//build .apk
ionic cordova build --release android

cordova emulate android

```

cordova run android失敗可以嘗試移除./platforms/android/.gradle目錄

### real device : The connection to the server was unsuccessful. (http://140.254.160.122:8100)
- ionic cordova run android --prod --livereload
- 似乎與Splashscreen有關

```
//config.xml
    <platform name="android">
        <preference name="loadUrlTimeoutValue" value="700000" />
    </platform>
```

### real device : PLUGIN\_NOT\_INSTALLED
```js
import { Platform } from 'ionic-angular';
import { AndroidPermissions } from '@ionic-native/android-permissions';
import { SpeechRecognition } from '@ionic-native/speech-recognition';

this.platform.ready().then((readySource) => {
    alert('Platform ready from'+readySource);
    // Platform now ready, execute any required native code
          
    this.androidPermissions.checkPermission(this.androidPermissions.PERMISSION.RECORD_AUDIO).then(
        success => alert('Permission granted'),
        err => alert('Permission not granted')
    );
    
    this.speechRecognition.isRecognitionAvailable()
        .then((available: boolean) => alert('1?'+available))
        
    this.speechRecognition.startListening({})
        .subscribe(
            (matches: Array<string>) => alert('2?'+matches),
            (onerror) => alert('error:'+ onerror)
        )      
});
```
### 實測裝置
- device要開啟開發人員模式 與 usb debug 與 wifi(與server同網域)

### app nav tab view

app 本身有一預設的nav
tab也算一個nav (但tabs不是)

### ion-tab
本身算一個可nav元件,url名稱為tag上的**tab-Title**,不設定會變tab-0

### IonicPage

```js
@IonicPage({
  name: 'my-page',      //ion-nav [root]=...對應的字串 (預設為元件名稱)
  segment: 'some-path'  //僅url顯示 與程式碼沒有連結 (預設為檔名)

  defaultHistory: ['my-page']
})

//linkpage.module.ts 與 linkpage.ts 檔名需一致,其檔名為預設的segment字串
```

### route

IonicPage與Componet類似,大概差在deeplink,但好像有bug

- IonicPage可用其component name的字串做nav (name:? segment:?)
- tab url路徑的目錄名稱是來自"tabComponentFilename/ion-tab的tabTitle/ComponentFilename"
- IonicPage url路徑名稱覆寫
- url還是會穿插一些內部的nav路徑 像是nav/n4/tabs/t0 [更新到3.6可以解決](https://github.com/ionic-team/ionic/releases)

```js

//ionic-app-scripts
cd node_modules/@ionic/app-scripts/dist

providers: [
    {provide: APP_BASE_HREF, useValue: "/"},
...

locationStrategy: 'path'//url上不使用#,但server要配合導向index.html

//page stack會保留,back不會reload,但url不會跳到pageurl
nav.push('...')
//每次back都會reload,但url會跳到pageurl
nav.setRoot('...');
```

### 調整slide
```css
.scroll-content{
  overflow-y: hidden;
}

.slide-zoom{
  height:100%;
  overflow-y: auto;
}
```

