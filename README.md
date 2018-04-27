# 筑波飯Bot

## 入力
- おおよその現在地
- おおよその予算
- 移動手段

## 出力
- おすすめの店（Google Place APIによる）をカルーセル表示


## 具体的処理

1. メニューボタンから地域を選択させる
1. 丁目を選択ボタン表示
    1.1 地域からGeocoding APIを使って、座標を取得
1. 予算選択ボタン表示
1. 交通手段選択ボタン表示
1. 得られた入力を引数にGoogle Place APIのnearbysearchを使って検索
1. 結果を5件ずつカルーセルで表示


## 環境
Anaconda python3

## 準備

`pip install -r requirements.txt`

あと適宜足りなかったら pip でインストール


### 環境変数の設定

#### IBM Cloudのアカウントを作成ログイン

[login url](https://idaas.iam.ibm.com/idaas/mtfim/sps/authsvc?PolicyId=urn:ibm:security:authentication:asf:basicldapuser)
create resource -> cloud foundry app -> python

#### .envの作成
.env.sampleを.envにリネームし編集する。その際、各種APIを以下から取得

- API各種
    - https://developers.line.me/console/
    - https://developers.google.com/places/web-service/?hl=ja
    - https://developers.google.com/maps/documentation/geocoding/get-api-key?hl=ja
- IBM Cloudの環境変数はここから変更
    - ibm cloud -> select your App -> Connections -> Cloudant -> View Credentials
- Cloudant NoSQL DBに作ったdbの名前にする
    - DB_NAMEに設定する

#### ibm cloudに環境変数の設定をする

[cloudfoundry/cli download](https://github.com/cloudfoundry/cli/releases)

`generate_shell_script_for_set-env.py`内のIBM Cloudのユーザー情報を変更する。

```bash
python generate_shell_script_for_set-env.py
sh set-env.sh
```
を実行すると、先程編集した.envを元に環境変数が設定できる。

### vcap-local.jsonの準備
- ibm_cloud(dashboard) -> Cloudant NoSQL DB -> show credentials　をみて適宜コピペ


```json
{
 "services": {
   "cloudantNoSQLDB": [
     {
       "credentials": {
         ここに適宜追記
       },
       "label": "cloudantNoSQLDB"
     }
   ]
 }
}
```

## 実行方法


ngrokを使う場合,
ngrok.exeを起動して、`ngrok.exe http 8000`
webhook url を設定して、
- https://xxxxxxxx.ngrok.io/line/callback

`python app.py`


## IBM Cloud へのPush

あるいは最初からGithubからのCIを設定した方がいい。その場合、Gitを含んだToolchainを作り、Git部分をGithubに変える。

1. [cloudfoundry cli](https://github.com/cloudfoundry/cli#downloads "cloudfoundry/cli: The official command line client for Cloud Foundry")をインストール
1. bluemix アカウントを作成
1. [IBMが公開しているリポジトリ](https://github.com/IBM-Bluemix/get-started-python#3-prepare-the-app-for-deployment "IBM-Bluemix/get-started-python: A Python application and tutorial that use Flask framework to provide a REST API to receive requests from the UI. The API then persists the data to a Cloudant database.")のREADME.mdの3.以降を参照。

- Cloudfoundry CLI にバグがあるかもわからない。
    - 筆者環境ではインタラクティブにログインできなかった。その場合は以下参照。
    - http://cli.cloudfoundry.org/ja-JP/cf/login.html


## その他必要なこと。

- LINE Messaging APIを使うための諸準備。ググるべし。
    - 新規Botの場合、
        - Webhook使用
        - グループトーク機能On
        - 自動挨拶Offを忘れずに
- Google Place API, Google Map Geocoding APIを使うための準備。
    - Google Developer Consoleからプロジェクト作ったり、API有効化したり。

## 理解するのに必要であろう知識やスキル
- PCの基礎
- Python (+ Flask)
- エディタをそれなりに使える
- Webアプリケーションの基礎知識
    - post, get, port, html, css
        - 少しでいい。
- API, JSONの概念と、それを利用するスキル
- Git, Github, Continuous Integration の概念
- Bluemix(IBM Cloud), PaaSの概念。マニュアル読みながら使う。
- NoSQLの概念