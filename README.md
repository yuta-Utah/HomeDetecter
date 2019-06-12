# HomeDetecter

## 概要

Android端末のWiFi検出機能とAndroid端末ローカルのSQLite DBを用いて、Android端末が自宅・職場のWiFi情報を自動的に学習、自宅・職場にAndroid端末ユーザーがいるかどうかを簡単に判定するライブラリです。

## 動作環境

Android 4.3以降

## 利用方法

### app/build.gradle
アプリのapp/build.gradleに以下のコードを記述してください

```
repositories {    

    ...

    //本レポジトリです
    maven { url 'https://yuta-utah.github.io/HomeDetecter/repository/' } 
    
    ...
    
}


dependencies {

    ...

    //現在1.0.0が最新です
    api 'net.taparound.library:android-home-detecter:1.0.0'
    
    ...
    
}
```

### アプリ本体
```
//導入
import net.taparound.androidhomedetecter.*;

...

public class SomeApp ... {

    ...
    //インスタンスを取得
    HomeDetecterManager hdminstance = HomeDetecterManager.getInstance();

    ...

    //学習関数を起動(学習には数日かかることもあります)
    hdminstance.StartProcess(this);

    ...
    
    //学習関数を終了
    hdminstance.StopProcess(this);

    ...
    
    //自宅・職場判定関数
    Integer i1 = hdminstance.isWhere(this,1)  //第2引数 id : 1 の場合、自宅付近のWiFi情報との一致個数(0-7)を出力
    Integer i2 = hdminstance.isWhere(this,1)  //第2引数 id : 2 の場合、職場付近のWiFi情報との一致個数(0-7)を出力
    
    //利用例
    if(i1 > 2){ 
        Log.d("Response: ", "家にいると落ち着きます");
    } else if(i2 > 2 ){
        Log.d("Response: ", "お仕事がんばって!");
    } else {
        Log.d("Response: ", "ここはどこでしょう？");
    }
    
    ...
        
```

### 解説

+ 本ライブラリの学習用関数を起動すると、Android端末がWiFiスキャンを行うごとに(機種・設定にもよりますが、WiFiスイッチがONの場合、本ライブラリとは無関係に、自動的に15-60秒ごとにWiFiスキャンは行われます)、周辺のWiFi情報(WiFi基地局ごとにユニークなBSSIDを用います)を取得、ローカルのSQLite DBに蓄積し、その中で自宅・職場付近に存在すると思われるWiFi情報を選定します。

+ 自宅・職場付近にWiFiが存在すれば、同WiFiに接続できなくても、学習・判定には用いることが可能です。

+ 学習はアプリが起動しており、WiFiスキャンが行われている状態であれば、常に実行されます。学習をアプリ終了後も常時行いたい場合は、バックグラウンドプロセスから本ライブラリの学習関数を起動し、定期的にスリープ解除する等の対応が必要です。

+ 自宅の学習は午後10時から翌6時まで、職場の学習は午前10時から午後5時までの間に実施するため、学習には1日、長い場合だと数日かかる場合があります。なお、引っ越し等で自宅・職場が移転した場合、自宅・職場が更新されるまで1週間程度必要です。

+ 本ライブラリの自宅・職場判定関数を起動すると、起動時のAndroid端末のWiFiスキャン情報と、自宅・職場付近に存在するものとされたWiFi情報とを比較、WiFi情報が一致した個数(0-7)を返します。概ね一致個数が3以上となった場合、Android端末ユーザーは自宅・職場にいると考えられます。

+ 収集したWiFi電波環境情報はAndroid端末ローカルのSQLite DBにのみ蓄積され、自宅・職場の判定に利用されます。本ライブラリはインターネット接続はいたしません。

+ 利用に際しては、Androidの規約上、Android 6.0以降では、アプリに位置情報アクセスを許可する必要がありますが、位置情報へのアクセスは行いません。



