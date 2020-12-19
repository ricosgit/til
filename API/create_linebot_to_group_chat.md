# ライングループにコメントを投下するLINEbotを作成

## 手順
1. LINEbotを作成するために,LINEdevelopersに登録し、botを作成
2. GASでプログラムを作成し、グループIDを取得
3. GASでメッセージ送信用のプログラム作成
4. 作成したプログラムの動作タイミング設定
<br>

## LINEbotを作成するために,LINEdevelopersに登録し、botを作成
[コレ](https://developers.line.biz/ja/docs/messaging-api/getting-started/#using-console)を参考に設定。

※デフォルトではbotはライングループへの参加ができないようになっているので、
管理画面の「Messaging API」→「Allow bot to join group chats」をEnabledにしておく。

※デフォルトだとbotに対してメッセージを送ると「このbotは返信対応できませんよww」みたいなメッセージが返ってくるので、
これが送られないようにするために管理画面の「Messaging API」→「Auto Reply Message」→「Detailed settings」→「Auto-response」Disabledにしておく。
<br>

## GASでプログラムを作成し、グループIDを取得
LINE Messaging APIを使用するために、
Googleドライブで「新規」→「その他」→「Google Apps Script」でgsファイルを作成。
グループに対してメッセージを送るためにグループIDが必要となるが、LINEアプリからは参照できない。
そのため、botに対してメッセージを送信したタイミングでグループID（ついでにユーザーIDとルームIDも）を返す以下のプログラムを記述。

```js
var CHANNEL_ACCESS_TOKEN = "アクセストークン";

function doPost(e) {
  var contents = e.postData.contents;
  var obj = JSON.parse(contents)
  var events = obj["events"];
  for(var i = 0; i < events.length; i++){
    if(events[i].type == "message"){
      reply_message(events[i]);
    }
  }
}

function reply_message(e) {
  if (e.source.userId == null || e.source.userId == undefined) {
    var user_id = 'user_id=null';
  } else {
    var user_id = 'user_id='+ e.source.userId;
  }
  if (e.source.groupId == null || e.source.groupId == undefined) {
    var group_id = 'group_id=null';
  } else {
    var group_id = 'group_id='+ e.source.groupId;
  }
  if (e.source.roomId == null || e.source.roomId == undefined) {
    var room_id = 'room_id=null';
  } else {
    var room_id = 'room_id='+ e.source.roomId;
  }
  var ids = [user_id, group_id, room_id];
  var postData = {
    "replyToken" : e.replyToken,
    "messages" : [
      {
        "type" : "text",
        "text" : ids.join(",")
      }
    ]
  };
  var options = {
    "method" : "post",
    "headers" : {
      "Content-Type" : "application/json",
      "Authorization" : "Bearer " + CHANNEL_ACCESS_TOKEN
    },
    "payload" : JSON.stringify(postData)
  };
  UrlFetchApp.fetch("https://api.line.me/v2/bot/message/reply", options);
}
```

このプログラムをLINEbotに紐づけるために、WebhookURLを使用する。
GASのScript画面で、「公開」→「ウェブアプリケーションとして導入」→「更新(or作成)」して表示されたURLを
LINEbot管理画面の「Messaging API」→「Webhook settings」→「Webhook URL」に入力する。
その後「verify」を押して「Success」が表示されればOK。

※もし、「ボットサーバーから200以外のHTTPステータスコードが返されました」というエラーが返った場合は、
「ウェブアプリケーションとして導入」を行った際に「Who has access to the app:」が「Anyone, even anonymous」になっているか確認する。
<br>

## GASでメッセージ送信用のプログラム作成
上記で取得したチャネルトークンとグループIDを使用し、
LINE Messaging APIを叩くスクリプトを書く。
スクリプトを更新した際は「公開」→「ウェブアプリケーションとして導入」→「更新(or作成)」の手順を忘れずに。
```js
function notifyGarbageRemoval() {

  //urlはbotが一方的に送ってくるようにしたいので最後はpushになります。(返信用ならreply)
  var url = 'https://api.line.me/v2/bot/message/push';

  //botのチャネルトークンに関しては自分の作成したbotの管理画面のMessagingAPIの下の方にあります
  var channelToken = 'チャネルトークン';
  const youbi = ["日","月","火","水","木","金","土"];
  var text = "";

  //上で取得したグループID
  var to = "グループID"

  //Dateオブジェクト関連の処理の準備
  var date = new Date();
  date.setDate(date.getDate());
  var dayIndex = date.getDay();
  var day = ["日", "月", "火", "水", "木", "金", "土"];

  var messages = [];
  
  //--月・火・金は洗濯の日--//
  if (date.getHours() === 9) {
    if (day[dayIndex] == "月" || day[dayIndex] == "水" || day[dayIndex] == "金") {
      messages.push({
        'type': 'text',
        'text': '今日は洗濯の日やで',
      });
    }
  }

  //--メッセージがない場合は送信しない--//
  if (messages.length > 0){
    //--9時から23時の間は送信しない--//
    if (date.getHours() >= 9 && date.getHours() <= 23){
      
      UrlFetchApp.fetch(url, {
        'headers': {
          'Content-Type': 'application/json; charset=UTF-8',
          'Authorization': 'Bearer ' + channelToken,
        },
        'method': 'post',
        'payload': JSON.stringify({
          "to" : to,
          'messages': messages,
        }),
      });
    }
  }
}
```
<br>

## 作成したプログラムの動作タイミング設定
GASで作成したスクリプトの実行タイミングを設定する必要がある。（バッチみたいなもん）
GASのScript画面で、時計ボタン→「トリガーを追加」で実行タイミングを設定する。
今回は毎時間動かしたかったので、
```
イベントのソース: 時間主導型
時間ベースのトリガータイプ: 時間ベースのタイマー
時間の間隔を選択: 1時間おき
```
に設定した。


## 参考
[プログラマーとして初めて親の役に立つものを作った話(LINEbot) - Qiita]https://qiita.com/enomotoakira/items/64bf4efeccc0248958f1

[Messaging APIを始めよう | LINE Developers]https://developers.line.biz/ja/docs/messaging-api/getting-started/

[Google Apps Script のはじめ方【超初級編】｜Koushi Kagawa｜note]https://note.com/koushikagawa/n/n04aed663361f

[Google Apps Script試行錯誤 Blog: LINE BOTでuserId, groupId, roomIdを取得したい]https://www.pre-practice.net/2018/11/line-botuserid-groupid-roomid.html
