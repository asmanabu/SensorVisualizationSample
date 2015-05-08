# SensorVisualizationSample
WebSocket経由で受信したセンサーデータを[Google Gauge](https://developers.google.com/chart/interactive/docs/gallery/gauge)を利用して可視化するサンプルプログラムです。

本プログラムでは、[センサー・シミュレーター](https://quickstart.internetofthings.ibmcloud.com/iotsensor/)のデータを、[IBM Bluemix](https://console.ng.bluemix.net/)上にデプロイした[Node-RED](http://nodered.org/)を使用してWebSocketで出力したものを利用しています。

## 前提
1. Gitクライアントが導入されていること
2. cfツールが導入されていること
    * cfツールのインストールは、「Bluemixマニュアル」の["Cloud Foundry コマンド・ライン・インターフェースを使用したコーディングの開始"](https://www.ng.bluemix.net/docs/#starters/install_cli.html)を参照
3. IBM Bluemix上にNode-REDがデプロイされていること
    * Node-REDのデプロイ方法は、Bluemixマニュアルの["Creating apps with Node-RED Starter"](https://www.ng.bluemix.net/docs/#starters/Node-RED/nodered.html#nodered)を参照

## 作業の流れ
1. Node-REDのフローを作成
2. Githubからサンプルプログラムを取得
3. index.htmlを編集
4. manifest.ymlを編集
5. IBM Bluemixにサンプルプログラムをpush

## 1. Node-REDのフローを作成
Node-REDのフローエディターを起動し、以下のフローをインポートしてください。インポートの方法は、[Node-RED小ワザ集の"小ワザ1. フローのインポート"](http://qiita.com/asmanabu/items/d9ba9ff0fca745c8c216#%E5%B0%8F%E3%83%AF%E3%82%B61-%E3%83%95%E3%83%AD%E3%83%BC%E3%81%AE%E3%82%A4%E3%83%B3%E3%83%9D%E3%83%BC%E3%83%88)を参照してください。

```
[{"id":"bd2402cf.2fb78","type":"websocket-listener","path":"/ws/sensor","wholemsg":"false"},{"id":"184a0fa5.45eaa8","type":"ibmiot in","authentication":"quickstart","apiKey":"","inputType":"evt","deviceId":"0C60110016ea","applicationId":"","deviceType":"+","eventType":"+","commandType":"","format":"json","name":"IoT Sensor","service":"quickstart","allDevices":"","allApplications":"","allDeviceTypes":true,"allEvents":true,"allCommands":"","allFormats":"","x":99,"y":147,"z":"4b3f3f7f.65a9a8","wires":[["2f6b16a3.dca7e2"]]},{"id":"2f6b16a3.dca7e2","type":"function","name":"CreateMessage","func":"msg.payload =  {\n    \"temp\": msg.payload.d.temp,\n    \"humidity\": msg.payload.d.humidity,\n    \"objectTemp\": msg.payload.d.objectTemp\n};\n\nreturn msg;","outputs":1,"valid":true,"x":301,"y":147,"z":"4b3f3f7f.65a9a8","wires":[["242a56f5.2f771a","a967f901.ee1508"]]},{"id":"242a56f5.2f771a","type":"debug","name":"","active":false,"console":"false","complete":"payload","x":441,"y":63,"z":"4b3f3f7f.65a9a8","wires":[]},{"id":"a967f901.ee1508","type":"websocket out","name":"WebSocket Output","server":"bd2402cf.2fb78","client":"","x":532,"y":147,"z":"4b3f3f7f.65a9a8","wires":[]}]
```

インポート後、IBM IoTノードを編集し、[センサー・シミュレーター](https://quickstart.internetofthings.ibmcloud.com/iotsensor/)のMACアドレスを登録してください。

## 2. Githubからサンプルプログラムを取得
Githubからサンプルプログラムを取得します。

`git clone https://github.com/asmanabu/SensorVisualizationSample`

ここでは、C:\work\SensorVisualizationSample にcloneしたこととします。

## 3. index.htmlを編集
C:\work\SensorVisualizationSample\public\index.htmlの10行目を編集し、WebSocketのURLを記入します。

```
var wsUrl = '<Your WebSocket URL>';
```

`<Your WebSocket URL>`の部分を修正します。WebSocketのURLは、Node-REDをデプロイしたBluemixのURLに`/ws/sensor`を追加したものとなります。たとえば、Node-REDをデプロイしたURLが`http://iotfvisualizationsample.mybluemix.net`であれば、以下のURLとなります。`ws/sensor`の部分は、NodeREDのWebSocket Outputノードにて定義していますので、確認してみてください。

```
http://iotfvisualizationsample.mybluemix.net/ws/sensor
```

## 4. manifest.yml を編集
manifest.yml は、Bluemixにアプリケーションをデプロイするときに参照されるファイルです。
アプリケーションが使用するホスト名は、Bluemixを利用する全ユーザーで重複が許されないため、ここで編集します。

エディターで C:\work\SensorVisualizationSample\manifest.yml を開きます。
*\<change to your hostname\>* の部分を修正してください。

## 5. IBM Bluemixにサンプルプログラムをpush
サンプルプログラムをBluemixにデプロイします。

C:\work\SensorVisualizationSampleに移動し、`cf push`を実行します。
現在のディレクトリ以下にあるすべてのファイルをBluemixに転送します。**不要なファイルを転送しないように、必ずディレクトリを移動してください。**
