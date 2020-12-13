# youtubeでOAuth認証を使用する
YOUTUBE DATA APIを使用して動画の字幕データを取得しようとしたところ、
OAuth認証が必要とのことで対応。
<br>

## 設定手順
詳しくは
[OAuth 2.0 を使って YouTube Data API を叩くまでの手順 | Developers.IO]https://dev.classmethod.jp/articles/oauth2-youtube-data-api/
を参考に。

1. Google Cloud Platformでプロジェクト作成
2. 「ライブラリ」画面で**YouTube Data API v3**を有効化
3. 「OAuth同意画面」で同意画面の設定
3. 「認証情報」画面でOAuthクライアントIDを作成
4. `composer require google/apiclient:~2.0`を実行し、必要なライブラリをインストール
5. [PHPコードサンプル]https://developers.google.com/youtube/v3/code_samples/php?hl=ja#updating_a_video_by_adding_new_tags を参考にコーディング
<br>

## コードサンプル
自分のチャンネルのタイトルリストを表示したい場合
```php
<?php

/**
 * Library Requirements
 *
 * 1. Install composer (https://getcomposer.org)
 * 2. On the command line, change to this directory (api-samples/php)
 * 3. Require the google/apiclient library
 *    $ composer require google/apiclient:~2.0
 */
// if (!file_exists(__DIR__ . '/vendor/autoload.php')) {
//   throw new \Exception('please run "composer require google/apiclient:~2.0" in "' . __DIR__ .'"');
// }

// require_once __DIR__ . '/vendor/autoload.php';
session_start();

/*
 * You can acquire an OAuth 2.0 client ID and client secret from the
 * Google API Console <https://console.developers.google.com/>
 * For more information about using OAuth 2.0 to access Google APIs, please see:
 * <https://developers.google.com/youtube/v3/guides/authentication>
 * Please ensure that you have enabled the YouTube Data API for your project.
 */
$OAUTH2_CLIENT_ID = ''; // ここに発行したクライアントIDを入力
$OAUTH2_CLIENT_SECRET = ''; // ここに発行したクライアントシークレットを入力

$client = new Google_Client();
$client->setClientId($OAUTH2_CLIENT_ID);
$client->setClientSecret($OAUTH2_CLIENT_SECRET);
$client->setScopes('https://www.googleapis.com/auth/youtube');
$redirect = filter_var('http://' . $_SERVER['HTTP_HOST'] . $_SERVER['PHP_SELF'],
  FILTER_SANITIZE_URL);
$client->setRedirectUri($redirect);

// Define an object that will be used to make all API requests.
$youtube = new Google_Service_YouTube($client);

// Check if an auth token exists for the required scopes
$tokenSessionKey = 'token-' . $client->prepareScopes();
if (isset($_GET['code'])) {
  if (strval($_SESSION['state']) !== strval($_GET['state'])) {
    die('The session state did not match.');
  }

  $client->authenticate($_GET['code']);
  $_SESSION[$tokenSessionKey] = $client->getAccessToken();
  header('Location: ' . $redirect);
}

if (isset($_SESSION[$tokenSessionKey])) {
  $client->setAccessToken($_SESSION[$tokenSessionKey]);
}

// Check to ensure that the access token was successfully acquired.
if ($client->getAccessToken()) {
  try {
    // Call the channels.list method to retrieve information about the
    // currently authenticated user's channel.
    $channelsResponse = $youtube->channels->listChannels('contentDetails', array(
      'mine' => 'true',
    ));

    $htmlBody = '';
    foreach ($channelsResponse['items'] as $channel) {
      // Extract the unique playlist ID that identifies the list of videos
      // uploaded to the channel, and then call the playlistItems.list method
      // to retrieve that list.
      $uploadsListId = $channel['contentDetails']['relatedPlaylists']['uploads'];

      $playlistItemsResponse = $youtube->playlistItems->listPlaylistItems('snippet', array(
        'playlistId' => $uploadsListId,
        'maxResults' => 50
      ));

      $htmlBody .= "<h3>Videos in list $uploadsListId</h3><ul>";
      foreach ($playlistItemsResponse['items'] as $playlistItem) {
        $htmlBody .= sprintf('<li>%s (%s)</li>', $playlistItem['snippet']['title'],
          $playlistItem['snippet']['resourceId']['videoId']);
      }
      $htmlBody .= '</ul>';
    }
  } catch (Google_Service_Exception $e) {
    $htmlBody = sprintf('<p>A service error occurred: <code>%s</code></p>',
      htmlspecialchars($e->getMessage()));
  } catch (Google_Exception $e) {
    $htmlBody = sprintf('<p>An client error occurred: <code>%s</code></p>',
      htmlspecialchars($e->getMessage()));
  }

  $_SESSION[$tokenSessionKey] = $client->getAccessToken();
} elseif ($OAUTH2_CLIENT_ID == 'REPLACE_ME') {
  $htmlBody = <<<END
  <h3>Client Credentials Required</h3>
  <p>
    You need to set <code>\$OAUTH2_CLIENT_ID</code> and
    <code>\$OAUTH2_CLIENT_ID</code> before proceeding.
  <p>
END;
} else {
  $state = mt_rand();
  $client->setState($state);
  $_SESSION['state'] = $state;

  $authUrl = $client->createAuthUrl();
  $htmlBody = <<<END
  <h3>Authorization Required</h3>
  <p>You need to <a href="$authUrl">authorize access</a> before proceeding.<p>
END;
}
?>

<!doctype html>
<html>
  <head>
    <title>My Uploads</title>
  </head>
  <body>
    <?=$htmlBody?>
  </body>
</html>

```
<br>

## 「このアプリは確認されていません」と表示された時
上記OAuthを実装し、「このアプリは確認されていません」と言う画面が表示された場合、
`詳細` を押した後、`許可` するとページが表示されるようになる。
ちなみにこの画面を表示させないようにするにはGoogleへ申請を出す必要がありそう（めんどくさそう）

## 参考
[OAuth 2.0 を使って YouTube Data API を叩くまでの手順 | Developers.IO]https://dev.classmethod.jp/articles/oauth2-youtube-data-api/
[PHP コード サンプル  |  YouTube Data API  |  Google Developers]https://developers.google.com/youtube/v3/code_samples/php?hl=ja#updating_a_video_by_adding_new_tags
[Google Apps Scriptで「このアプリは確認されていません」と表示されたときの対処方法  |  You Look Too Cool]https://stabucky.com/wp/archives/10804