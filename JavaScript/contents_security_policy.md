# Contents Security Policy


## Contents Security Policyとは
クロスサイトスクリプティング (XSS) 、データインジェクション、クリックジャッキング、パケットキャプチャなどブラウザに表示されるコンテンツを用いた、よく知られた種類の攻撃を検出して軽減するするために追加されたセキュリティレイヤー。

サーバサイドからブラウザに対してコンテンツの使用ポリシーを伝えて各種攻撃を回避する。
CSP を記述することで、コンテンツの提供元や取得方法（HTTPS経由のみ取得可能など）を制限することができ、コンテンツ提供者が意図しないコンテンツを読み込ませることを阻止することができる。


##　設定方法
httpヘッダーの場合
```
Content-Security-Policy: [ディレクティブ] [オリジン] [オリジン]; [ディレクティブ] [オリジン] [オリジン]; ...
```


metaタグの場合
```html
<meta http-equiv="Content-Security-Policy" content="[ディレクティブ] [オリジン] [オリジン]; [ディレクティブ] [オリジン] [オリジン]; ...">
```

書き方例
```html
<meta http-equiv="Content-Security-Policy" content="script-src https://host1.com https://host2.com;">
```


## オリジンの指定
|名称|例|
|--|--|
|スキーム|data:, https:|
|ホスト名|example.com|
|完全修飾URI|https://example.com:443|
|ワイルドカード|*://*.example.com:*<br>スキーム、ポート、ホスト名の一番左端のみに限定|


## キーワードによるオリジンの指定
|キーワード|説明|
|--|--|
|'none'|何も一致しない = 全て無効化|
|'self'|現在のオリジンと一致する<br>サブドメインは除外|
|'unsafe-inline'|インラインJavaScriptおよびCSSを許可する|
|'unsafe-eval'|evalなどのtext-to-JavaScriptの仕組みを許可する|


## ディレクティブの指定
|ディレクティブ|説明|
|--|--|
|script-src|スクリプト関連の権限を制限する|
|base-uri|<base>要素に表示できるURLを制限する|
|child-src|ワーカーと組み込みのフレームコンテンツのURLを制限する|
|connect-src|XHR, WebSockets, EventSourceを経由して接続できるオリジンを制限する|
|font-src|ウェブフォントを配信できるオリジンを制限する|
|form-action|<form>タグからの送信の有効なエンドポイントを制限する|
|frame-ancestors|現在のページに組み込める参照元を制限する<br><frame>,<iframe>,<embed>,<applet>タグに適用される|
|img-src|画像を読み込み可能なオリジンを制限する|
|media-src|動画と音声を配信できるオリジンを制限する|
|object-src|Flashなどのプラグインの種類を制限する|
|plugin-types|ページで起動できるプラグインの種類を制限する|
|report-uri|コンテンツセキュリティポリシーが違反された時にレポートを送信するURLを指定する|
|style-src|スタイルシートのscript-srcに相当する|
|upgrade-insecure-requests|ユーザーエージェントに支持してURLスキーマを書き直し、HTTPをHTTPSに変更する|
|default-src|未指定の-srcディレクティブの大半に対してデフォルトを定義する|


## 参考
[Content-Security-Policyの概要メモ - Qiita]view-source:https://qiita.com/zabu/items/d2fbac1abc81eba38efb
[Content Security Policy - Mozilla | MDN]https://developer.mozilla.org/ja/docs/Mozilla/Add-ons/WebExtensions/Content_Security_Policy
[Contents Security Policy（CSP）のお勉強 - Qiita]https://qiita.com/o_Ozzzzk/items/c505b7dd20707eb4ee08