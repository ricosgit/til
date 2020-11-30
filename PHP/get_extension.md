# ファイル名から拡張子を切り出す



## 結論
ファイル名のうち、「.（ドット）」以降を取得すれば良い。
```php
$filename = 'sample.pdf';
$ext = substr($filename, strpos($filename, '.') + 1);
```

ドットも含めて取得したい場合は以下
```php
$filename = 'sample.pdf';
$ext = substr($filename, strpos($filename, '.'));
```



## 解説
ファイルネーム内で「.」が含まれる位置を取得（strpos()）
.の次の文字（+1しているため）からそれ以降の文字（substrの第3引数を指定しないと指定した位置から最後まで取得する）



## substr()
```
substr ( string $string , int $start [, int $length ] ) : string|false
```
文字列 string の、start で指定された位置から length バイト分の文字列を返す。



## strpos()
```
strpos ( string $haystack , mixed $needle [, int $offset = 0 ] ) : int
```
文字列 haystack の中で、 needle が最初に現れる位置を探す。



## 参考
[PHPでファイル名文字列から拡張子のみ取得する時に最も速いのはどの方法か - Qiita]https://qiita.com/kijtra/items/d25679684a46b8f8dcfd
[PHP: substr - Manual ]https://www.php.net/manual/ja/function.substr.php
[PHP: strpos - Manual ]https://www.php.net/manual/ja/function.strpos.php