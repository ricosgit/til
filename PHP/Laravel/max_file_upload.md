# ファイルアップロード時に「ファイルのアップロードに失敗しました」と出る
<br>

## 事象
Laravelバリデーションで特に設定していないが、ファイルアップロード時に
```
ファイルのアップロードに失敗しました
```
というエラ〜メッセージが表示される。
<br>

## 原因
php.iniで設定されている `max_file_uploads` の値を超えていることが原因。
アップロード時にこのファイル容量を超えていると自動でLaravelのバリデーションが機能する。

## 対応方法 
`max_file_uploads` の上限を上げてやれば良い。
対応方法は以下の3つ。
1. php.iniを編集する。
2. .htaccess内に記述する。
3. httpd.confに記述する
<br>

### php.iniを編集する
vimなどのテキストエディタでphp.iniを開き、
「File Uploads」の箇所の「upload_max_filesize」の値を変更
```php.ini
upload_max_filesize = 8M
```

### .htaccess内に記述する
以下の行を追加
```.htaccess
php_value upload_max_filesize "8M"
```

### httpd.confに記述する
.htaccessと同様
```httpd.conf
php_value upload_max_filesize "8M"
```

## 参考
[Laravelでファイルアップロードが失敗する場合の対処法 - Qiita]https://qiita.com/sano1202/items/b2babd55a6b11109de9a
[PHPのファイルアップロードサイズの上限値を変更する - Qiita]https://qiita.com/katsukii/items/243d24ad81c787b14d4d